---
title: "OBTP Tester Script (Python)"
date: 2022-09-14T16:10:02+10:00
draft: false
tags: ["obtp","cisco","python","api","scripting"]
---

Have you ever needed a simple tool to push a One Button to Push (OBTP) meeting to a Cisco video endpoint (like a DX series), for testing purposes? If your answer is **'yes'**, then you should read on to find out how you can make use of a script that I have written for exactly that purpose.

![Calendar Pop Up](/post/2022-obtp-tester-cli/dx80-popup.png#center50 "Calendar pop up on DX70")


## Overview
This project is a way to push a single booking to a Cisco video endpoint running CE software, for the purposes of testing and demonstration. This should work on almost any Cisco endpoint running the CE series software. Written in Python 3, it is designed to be a tool that you keep on your machine in the event that you need to verify if the One Button to Push (OBTP) feature of the endpoint is working, or, if you need to push a dummy booking e.g. for the purposes of producing documentation.

Please take note of the discalimer below:

---

**Important Disclaimer:**
>* This is an example only for educational and/or testing purposes and is intended for personal and non-commercial use only.
>* This is not a calendaring solution nor is it intended to be used as one.
>* The API used in the code is protected by Cisco and should not be used commercially as per the Permitted Commercial Use for Scheduled Meeting Join statement in the published API guide, see Page 23, here: https://www.cisco.com/c/dam/en/us/td/docs/telepresence/endpoint/ce915/collaboration-endpoint-software-api-reference-guide-ce915.pdf

---

**Points to note:**

* Every test booking you make with the tool will overwrite all previous bookings, so please take care not to use this on a production Cisco endpoint, leave it for your lab systems.
* Script has undergone limited testing, it has worked on a number of endpoints but as a pet project, don't expect it to be perfect.
* Inspired by the excellent work done by Nick M (acaeti) who has built an excellent OBTP tester: https://github.com/acaeti/OBTP-emulator (go and check his repo out).

## Pre-requisites

* Minimum of Python 3.7 installed on your machine.
* Python added to `PATH`.
* Python requests installed on your machine.
* Download `cli.py` to your machine from my GitHUb repo: https://github.com/aledmorris/obtp_tester

If you are unsure of how to install Python and add it to `PATH` then go ahead and Google how to do that and then come back - there are many excellent articles online which explain how to do this.

Once Python is installed correctly, the next step is to install requests and depending on which OS you use it may be different.

**Windows:**

```powershell
pip install requests
```

**Mac:**

```shell
pip3 install requests
```

The script includes some default values which you will need to change to suit your environment. These are pretty self explanatory. `ep_url` represents the IP address of your endpoint:

```python
# set endpoint FQDN or IP address
ep_url = 'https://192.168.1.9/'
# URL for authentication
session_auth = ep_url+'xmlapi/session/begin'
# URL for making bookings
bookings_url = ep_url+'bookingsputxml'

# Default booking values
firstname = 'Aled'
lastname = 'Morris'
email = 'aled@example.com'
start_buffer = '300' # secs
end_buffer = '0' # secs
proto = 'SIP'
call_rate = '4096'
call_type = 'Video'
uri = 'test_call@example.com'
```

Ensure you DO NOT change the values of `session_auth` or `bookings_url`, otherwise it will not work.

## Run cli.py

Change directory to the folder where the script is located, then issue the following command.

**Windows:**

```powershell
python cli.py
```

**Mac:**

```shell
python3 cli.py
```

The script should run and you should be presented with the following screen:
![Script](/post/2022-obtp-tester-cli/1-starting.png#center "Script")

Enter the username and password of your endpoint, the script will then attempt to authenticate. Assuming you entered the correct credentials then the script will authenticate successfully which under the hood means it has obtained a session token to be used for any API commands to be made to the endpoint.

The menu is displayed and you need to press `1` to make a test booking.
![Menu options](/post/2022-obtp-tester-cli/2-menu.png#center "Menu options")

Give the booking a title and enter the date and time of the booking, note the time is entered in UTC timezone. However, the default value for the date field is the current date (UTC) and the script tells you the current date/time in UTC to make it easier to work out what the time you want the booking to start. Finally enter the duration for the booking, measured in minutes.
![Booking details](/post/2022-obtp-tester-cli/4-enter-booking.png#center "Booking detail")

Once the details are entered, the XML payload used in the bookings API command to the endpoint is displayed, it helps to verify what was sent. The response code of the request is shown, where 200 indicates a successful request. At this point you should expect your booking to be loaded onto the endpoint and show on the main user interface.
![Cisco User Interface](/post/2022-obtp-tester-cli/dx80-main-entry.png#center "Booking showing on endpoint user interface")



