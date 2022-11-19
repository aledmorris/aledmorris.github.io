---
title: "Monitoring Pexip Infinity with Uptime Kuma Part 1 - Introduction"
date: 2022-11-17T01:00:00+10:00
draft: false
tags: ["docker","monitoring","pexip"]
---

Uptime Kuma is an effective, self-hosted, monitoring tool for services on your network with a very useful notification system. I stumbled upon it when exploring [Docker](https://docker.com) based home lab services and given its feature set thought it could be used to monitor [Pexip](https://www.pexip.com) Infinity deployments quite effectively. In this post (and others) I'll outline some of the specific features which can make it particularly suitable for monitoring Infinity.

## Uptime what?
It's not a full-fledged enterprise monitoring system but if you are a Pexip Infinity administrator it is a relatively simple way to monitor the platform and to get notifications, in case of any problems, into a number of services like Microsoft Teams, Slack, and a few more.

Some of the key features are:
- Many different monitoring methods, allowing some creative ways of monitoring.
- Status timelines.
- Many notification possibilities but crucially, Teams, Slack and SMTP are included.
- Ability to publish multiple status pages.

![Uptime Kuma example page](/post/2022-uptimekuma-pt1/example-entry.png#center "Uptime Kuma example page")

There are so many more, some of which we will see as we go, here is the official GitHub page for Uptime Kuma is here: https://github.com/louislam/uptime-kuma

## Why montior Pexip Infinity?
Proactively monitoring services should be something that you do as a service owner because that can afford you the ability to catch and investigate an issue before users are effected. Take the example of getting an alert in the morning at 8.30am that a particular system or service is down, it may be that you have time to investigate and remedy the issue, or, to let users know there is an outage and will be impacted before they try to use the service thus allowing them to make alternative plans.

In a lot of cases, that's all that you need. For some scenarios notifications into your internal chat tools will be fine, in some scenarios you could choose to send the notification via email to a relevant email distribution list. As you can publish status pages, no reason why you couldn't show that on a screen in your NOC.

See below an example of the kind of notifications you would receive, first example is in Slack:

![Uptime Kuma notification in Slack](/post/2022-uptimekuma-pt1/slack-notification1.png#center "Uptime Kuma notification in Slack")

Here is what a notification looks like in Microsoft Teams:

![Uptime Kuma notification in Microsoft Teams](/post/2022-uptimekuma-pt1/teams-notification1.png#center "Uptime Kuma notification in Microsoft Teams")

Each shows a simple message indicating when a service has gone down with a subsequent message appearing should that service come back online.

Note that you need to create notification types separately to the monitors themselves, they do not get created automatically. Read some documentation or watch a YouTube video to learn how to set up integrations to your chosen platforms. You would then configure the monitor to use one of the configured notification methods.

## How to get started?
Uptime Kuma is available as a [Docker](https://www.docker.com/) container, which I tested with (because I love containers), or is available as a non-Docker install. You could host it on something like a Raspberry Pi with Docker. Alternatively, you could spin up a small Linux server VM and use Docker on that. The barrier for entry is relatively low.

There are a number of quality YouTube videos which cover topics such as installation and detailed configuration of the service, the best one I have found is by [Techno Tim](https://youtu.be/r_A5NKkAqZM). This series of posts is intended to give ideas on how you could potentially use Uptime Kuma with Pexip Infinity, to get the ideas flowing.

## Monitoring the Management Node
The first example is to monitor the Management node in Pexip Infinity, to do this you can use the most simple of monitor available in Uptime Kuma. The management node in the Pexip deployment is a web administration UI where all of the admin related tasks are done, so nothing elaborate is needed.

The monitor type which can be used is **HTTP(S)** which will make an HTTP or HTTPS request to the specified address. If Uptime Kuma receives a response, typically a **200 OK**, then the host will be deemed to be up.

You can give the monitor a name and specify the URL of the service using the FQDN or an IP address.

![Uptime Kuma HTTPS Monitor settings](/post/2022-uptimekuma-pt1/https-settings1.png#center "Uptime Kuma HTTPS Monitor settings")

The **Heartbeat interval** is how often the host will be checked in seconds. Rather than classify a service as down immediately on a failed status check you can specify the number of retries to attempt before alerting that it is down. The **Heartbeat Retry Interval** allows the retry status checks have a different interval than the regular status checks.

![Uptime Kuma HTTPS Advanced Monitor settings](/post/2022-uptimekuma-pt1/https-settings2-adv.png#center "Uptime Kuma HTTPS Advanced Monitor settings")

The advanced options allow for some useful features, such as notifying if the TLS cert of the host is expiring or expired, and to ignore any untrusted certificates, like self-signed certs. By default a response code in the range from 200 to 299 will be considered as up, but this can be changed if you wish. Tagging the entry is a useful way to categorise hosts. Once the entry is saved it will be added to the list and monitoring will begin. Each green dot represents a successful status check. 

![Uptime Kuma status timeline](/post/2022-uptimekuma-pt1/green-dots.png#center "Uptime Kuma status timeline")

Clicking into a particular monitor you will see a graph representing the request response times and some other useful metrics.

![Uptime Kuma monitor metrics](/post/2022-uptimekuma-pt1/https-metrics.png#center "Uptime Kuma monitor metrics")

## Next Steps

This post is introductory and shows the most basic of HTTP monitors that can be used, which in itself, can be quite useful. Fututre posts in this series will look at the other monitor types in Uptime Kuma and how they can monitor in unique and powerful ways.

[Monitoring Pexip Infinity with Uptime Kuma Part 2 - DNS Monitoring]({{< ref "/post/2022-uptimekuma-pt2-dns/index.md" >}}), is the next post in this series.