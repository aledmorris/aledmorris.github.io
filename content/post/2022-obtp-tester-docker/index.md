---
title: "OBTP Tester with Web UI (Docker)"
date: 2022-11-18T01:00:00+10:00
draft: false
tags: ["obtp","cisco","python","api","scripting","docker"]
---

Previously, I have created a OBTP Tester Python script, which is covered in my post, [OBTP Tester Script (Python)]({{< ref "/post/2022-obtp-tester-cli/index.md" >}}). I took the OBTP Tester Python script and gave it a Web UI and containerized it using [Docker](https://docker.com). The YouTube video below demonstrates how to use it.

{{< youtube C3q2roZXpdE >}}

I wanted to create my first Docker container and this seemed like an ideal project to try it with. It turned out very well and I published the image to the Docker Hub. A nice little project to get my feet wet with Docker but also to practice some front end work ([Bootstrap](https://getbootstrap.com/)) and to practice [Flask](https://flask.palletsprojects.com/en/2.2.x/).

A OBTP calendar entry has a lot of fields to fill out and I wanted to make it easier for people to use on a regular basis and not have to fill out those details all of the time, that's where the use of a variables file comes in. The variables file is passed into the container and the variables are used as environment variables which the ONBTP tester uses to populate default values in the fields. Assuming that you have Docker installed on your machine then all you need to do is run the container, the variables file is optional.

I felt very satisfied being able to achieve my goal of creating my first meaningful container and making it available for everyone. When you have a real use case which is not overly complex it makes for a great learning vehicle.

If you want to try it out see the listing in Docker Hub for full instructions: [aledmorris/obtp-tester-web](https://hub.docker.com/r/aledmorris/obtp-tester-web)

Please do take note of the disclaimer which comes with using it:

---

**Important Disclaimer:**
>* This is an example only for educational and/or testing purposes and is intended for personal and non-commercial use only.
>* This is not a calendaring solution nor is it intended to be used as one.
>* The API used in the code is protected by Cisco and should not be used commercially as per the Permitted Commercial Use for Scheduled Meeting Join statement in the published API guide, see Page 23, here: https://www.cisco.com/c/dam/en/us/td/docs/telepresence/endpoint/ce915/collaboration-endpoint-software-api-reference-guide-ce915.pdf

---