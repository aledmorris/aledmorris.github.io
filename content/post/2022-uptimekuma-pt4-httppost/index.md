---
title: "Monitoring Pexip Infinity with Uptime Kuma Part 4 - HTTP(S) Monitoring (Advanced)"
date: 2022-12-28T01:00:00+10:00
draft: false
tags: ["docker","monitoring","pexip","https","post","webrtc"]
---

In this part (4) of **Monitoring Pexip Infinity with Uptime Kuma** we will take a look at the **HTTP(S)** monitor type again but using some additional options. If you have not yet read part three, [Monitoring Pexip Infinity with Uptime Kuma Part 3 - HTTP(S) Keyword Monitoring]({{< ref "/post/2022-uptimekuma-pt3-httpskw/index.md" >}}), then go back and check that out first.

A common question which is asked about [Pexip](https://www.pexip.com) Infinity is how to check that a conferencing node is online but also is in a state where it is able to facilitate calls. Uptime Kuma does not have a built in video client so it cannot make test calls for you, although the HTTP(S) monitor can be used with some additional options to check that a node is capable of receiving a WebRTC call.

## Overview
The Pexip client REST API can be used to make a HTTPS request to a conference node, the same type of request it makes to begin a call via WebRTC, to request a token. The conference node should respond with a `200 OK` and the requested token, indicating the node is up and is ready to take a call. Short of making an actual SIP call then this is the best indicator that a node is available for calls.

- The token request is part of the client REST API, details can be found here: https://docs.pexip.com/api_client/api_rest.htm#request_token.

During an actual WebRTC call, Pexip expects the client to refresh the token periodically, but must be done within 120 seconds or the call will timeout and be dropped. This is not required for the test being done with Uptime Kuma as we just want to check if the service is alive and capable, not to have an ongoing call. A side effect of this is that the request being made is shown as a call attempt and will show up in the Live View for 120 seconds until the call is timed out. For this reason, there a couple of point to note:

- The **Heartbeat Interval** for the monitor must be greater than 120 seconds, otherwise the system will have overlapping test calls and could add confusion to the live view - no need to muddy one of the administrative view points of Pexip.
- Create a test call service to test against and to label it appropriately e.g including reference to Uptime Kuma, so the admin knows it is one of the test probes. Test calls are designed for users to test their audio and video and will then auto disconnect after the test is complete and is therefore short lived - better suited for the task of monitoring.

## Monitor Configuration
The HTTPS monitor should be used to point to a conferencing node and the **Heartbeat Interval** should be set to your preferred value, but must be greater than 120 seconds. The URL needs to be specific to the test call service you are testing against. 

- Append the following to the URL: `/api/client/v2/conferences/<test call alias>/request_token`
- Ensure you change **\<test call alias\>** to be the alias of your test call service. 
 
![HTTPS Montitor Configuration](/post/2022-uptimekuma-pt4-httppost/https-mon1.png#center "HTTPS Montitor Configuration")

In my test the test call service alias is `edge.kuma`, therefore mine would be `/api/client/v2/conferences/edge.kuma/request_token`.

![Test Call Alias Configuration](/post/2022-uptimekuma-pt4-httppost/test-call-alias.png#center "Test Call Alias Configuration")

For the monitor to work correctly, we need to set the HTTP method to **POST** and the body must contain a minimal payload for the probe to work. The payload uses only the required fields `display_name` and `call_tag`, example is below:

```
{
    "display_name": "Aled Uptime Kuma Test",
    "call_tag": "aled_kuma_test"
}
```

![HTTPS Montitor Payload](/post/2022-uptimekuma-pt4-httppost/payload.png#center "HTTPS Montitor Payload")

Save the monitor configuration and you should see a successful probe in Uptime Kuma, then the live view on Pexip will show the test call service. It will be shown on the live view for 120 seconds before disappearing because of the call timeout.

![Pexip Infinity Live View](/post/2022-uptimekuma-pt4-httppost/live-view.png#center "Pexip Infinity Live View")

## Conclusion
Using the HTTPS monitor in this way is a great way for an admin to have peace of mind that their conferencing nodes are up and are acapable of taking calls. It's a second step beyond just checking that the nodes are online, just because a node is online doesn't necessarily mean that the node can take a call.

Whether you set up a monitor probe for every indiviual conferencing node, or just monitor the published URL of the webapp is up to you.