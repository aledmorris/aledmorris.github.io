---
title: "Azure Logic Apps, Win and a Loss with Pexip Infinity Snapshots"
date: 2022-09-09T20:39:41+10:00
draft: false
tags: ["serverless","azure","pexip","api","cloud"]
---

This article was originally posted to my LinkedIn page, [here](https://www.linkedin.com/pulse/azure-logic-apps-win-loss-pexip-infinity-snapshots-aled-morris/).

![Header](/post/2022-azure-logic-apps-win-loss/az-header.png#center "Azure Logic App Designer")

My recent posts have been relating to serverless cloud, Azure Logic Apps specifically, and how I have been using them with Pexip Infinity's rich Management APIs. I thought I'd share with you a story of an initial win which ended up being a loss (or bust!). I'm sharing this as it provides context for when Logic Apps may be used and the type of tasks they are not suitable for.

## Problem to Solve

>
> Automate the retrieval and storage of diagnostic snapshots in an Azure Storage account.
>

The log bundle you retrieve from Pexip Infinity when you need to get support from us is the Diagnostic Snapshot. Not to be confused with a virtual machine snapshot, this is a log bundle which gives our support engineers 99% of all of the information they could need about your system for troubleshooting. The Pexip admin needs to manually retrieve a snapshot when they log a case with us so we can see what was going on at the time of the issue.

Sometimes a diagnostic snapshot does not cover the customer's reported issue, this could be due to a delay in the customer reporting the issue to us and if there is more than a few days lag then the logs may have rolled. If the customer had an archive of snapshots it would allow them to log a case and know they will have the snapshot on hand to cover any time period.

## API Call
In the Command API of Infinity there is an API call to trigger the creation of a diagnostic snapshot, the duration of the snapshot needs to be specified in the request body.

* [Command API](https://docs.pexip.com/api_manage/api_command.htm)
* [Taking a snapshot (example)](https://docs.pexip.com/api_manage/api_command.htm#snapshot)
 
 This API call is used as a key step in the Logic App, more details below.

## App Design

### Overview:

![App Design](/post/2022-azure-logic-apps-win-loss/az-flow1.png#center50 "Logic App Overview in Azure")

### Prerequisites:

* **Key Vault** - should contain the credentials for your Pexip Infinity Management node, both the admin username and the password itself, stored as secrets.
* **Storage Account** - an existing Blob container created to store the snapshots.

### Steps in the Logic App:

* **Recurrence (trigger)** - The app is run on a schedule e.g. every few hours, in my app this is every 4 hours.
* **Key Vault Get Secret** - Retrieve the credentials of my lab Management Node from my Azure Key Vault. This avoids my having to expose the actual credentials in my Logic App.
* **HTTP request** - making the API call to trigger the creation of the diagnostic snapshot. As I am triggering this flow every 4 hours, I take a 5 hour snapshot to give me a bit of a buffer.

![HTTP trigger action](/post/2022-azure-logic-apps-win-loss/az-http.png#center50 "HTTP action in Logic App.")

* **Create Blob V2 (Azure Blob Storage)** - converts the HTTP request body to a binary file and stores it in the specified container. Note the filename being set to include the UTC date and time as part of the file name for easy identification of when the snapshot was taken (this is the default filename when a snapshot is taken from the web admin GUI). The blob content takes the body returned from the HTTP request, converted to binary.

![Create BLOB](/post/2022-azure-logic-apps-win-loss/az-blob.png#center50 "Create BLOB in Logic App.")

## Winning
Testing against my lab was successful and the snapshot appears in the blob container as planned. The downloaded snapshot was extracted and validated to be a snapshot with all of the logs which are expected to be in there.

![Storage Account](/post/2022-azure-logic-apps-win-loss/az-storage-acct.png#center "Storage Account view in Azure.")

The downloaded snapshot was quite small as it's a lab system and there is minimal activity compared to a production system. To test the app with a larger file size, the snapshot duration was increased to 72 hours.

## Losing
Unfortunately, increasing the snapshot size broke the Logic App, ***the maximum file size which could be handled via the Logic App was 100 MB***. The reality in the real world is that customer snapshots are rarely less than 100 MB and can be up to a few GB in size. I was absolutely gutted when I learned about the limit as I had thought I had cracked a very important use case.

![HTTP action error](/post/2022-azure-logic-apps-win-loss/az-error.png#center50 "HTTP action error.")

## Conclusion
Logic Apps are excellent for automating Azure processes without the need to code. Being able to build with these blocks is attractive with a low barrier to entry in terms of learning and cost, the library of connectors is huge as well so the possibilities are massive. Archiving snapshots in such a simple manner would have been such a positive for all Pexip Infinity admins, particularly while using a service which is relatively low cost.

Here is what I learned from this experiment:

* Handling of very large files (>100 MB) is not a job for Logic Apps.
* Logic Apps are serverless and as such do not have a large amount of compute resource allocated to them, meaning that temporary storage is very low, which results in the limitations observed.
* Apps are more flexible than I had anticipated, the expressions and data manipulation is powerful.
* Think more about what I am trying to achieve with my app and does it fit into a serverless workflow? Is a Logic App appropriate for my use case or should I be looking towards an Azure Function or even a VM resource for the task?

My experiment was just that, seeing if it could work and it didn’t hold up to real world testing which was fine as it turned out to be the wrong tool for the job, and ***that is on me and not the fault of the tool***. Next time, I'll RTFM before diving in because maybe if I had I could have avoided the emotional rollercoaster this Logic App was.

----

## References
* [Limits and configuration reference for Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-limits-and-config?tabs=azure-portal)
* [Pexip - About the Management API](https://docs.pexip.com/api_manage/management_intro.htm)
* [Pexip - Downloading a diagnostic snapshot](https://docs.pexip.com/admin/diagnostic_snapshot.htm)
* [Pexip - Introduction to Pexip Infinity](https://docs.pexip.com/admin/admin_intro.htm)
