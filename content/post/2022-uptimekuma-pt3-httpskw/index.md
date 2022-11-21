---
title: "Monitoring Pexip Infinity with Uptime Kuma Part 3 - HTTP(S) Keyword Monitoring"
date: 2022-11-20T01:00:00+10:00
draft: false
tags: ["docker","monitoring","pexip","https","keyword","teams","cvi"]
---

In this part (3) of **Monitoring Pexip Infinity with Uptime Kuma** we will take a look at the **HTTP(s) - Keyword** type. If you have not yet read part two, [Monitoring Pexip Infinity with Uptime Kuma Part 2 - DNS Monitoring]({{< ref "/post/2022-uptimekuma-pt2-dns/index.md" >}}), then go back and check that out first.

The next monitor type to explore **HTTP(s) - Keyword**, this can be used in more than one way when monitoring [Pexip](https://www.pexip.com) Infinity. The URL which is monitored allows you to effectively check the webpage at the address for a particular keyword, it's quite useful and likely the idea behind this is to make website monitoring more effective.

![Uptime Kuma HTTPS Keyword Monitoring](/post/2022-uptimekuma-pt3-httpskw/https-keyword1.png#center "Uptime Kuma HTTPS Keyword Monitoring")

The regular HTTP(s) monitor could be used to monitor a web URL (see part one is this series of posts), but what if the web server is up and responding returning a `200 OK`, but the web page content is failing to render? This is where the **HTTP(s) - Keyword** type is great as your webpage would have some expected content on it, so you can check for the presence a particular word.

## Teams Alternative Dialling Information
When integrated to Microsoft Teams there are CVI specific details added to the Teams Meeting invite, including a link to a webpage, the **Alternative Dialling Instructions**, which shows all the different methods in which you can join the meeting via Pexip.

![Teams Meeting](/post/2022-uptimekuma-pt3-httpskw/teams-invite.png#center "Teams Meeting")

It is the public facing Infinity conferencing nodes which actually serve the page where this info is displayed to the user. Therefore, you could monitor the alternative dialling instruction pages to ensure they are available for your users.

## InstructionUri
As part of deploying a Teams Connector into Azure, one of the steps is to construct the URL used for the alternative dialling instructions, specifically the **InstructionUri** value, see the [Pexip Documentation](https://docs.pexip.com/admin/teams_connector.htm#authorize_lobby) for reference. The URL will be in the following format:

`https://<node_address>/teams/?conf={ConfId}&ivr=<alias>&d=<domain>&ip=<node_ip_address>&test=<test_call_alias>&prefix=<routing_rule_prefix>&w`

An example URL could be: `https://teamsaltjoin.example.com/teams/?conf={ConfId}&ivr=teams&d=example.com&test=test_call&prefix=teams.&w`

You can enter the URL into a browser to see what it looks like. An example of the rendered page is below:

![Teams Alternative Joining Instructions](/post/2022-uptimekuma-pt3-httpskw/alt-dialling.png#center "Teams Alternative Joining Instructions")

For this page you could simply use the keyword `interoperability`, as it only appears once on the page and it's part of the footer. It would therefore be a reasonable assumption that if that keyword is present then the joining instructions would have been loaded and be displayed above it. However, you could use any keyword which is rendered on the page.

## Monitoring Considerations
If you have more than one edge node in your deployment serving the alternative joining instructions, then it may be that for resiliency you have set up a single URL to load balance across multiple nodes, using DNS or other method. If you do this then you have a choice about how many URLs to actually monitor. You could just monitor the URL used in the **InstructionUri** and nothing else, i.e. the following:

- `https://teamsaltjoin.example.com/teams/?conf={ConfId}&ivr=teams&d=example.com&test=test_call&prefix=teams.&w`

In addition, you could monitor each edge node individually if you so wished by modifying the **InstructionUri** to reference each edge node directly, using an example:

- `https://edge01.example.com/teams/?conf={ConfId}&ivr=teams&d=example.com&test=test_call&prefix=teams.&w`
- `https://edge02.example.com/teams/?conf={ConfId}&ivr=teams&d=example.com&test=test_call&prefix=teams.&w`
- `https://edge03.example.com/teams/?conf={ConfId}&ivr=teams&d=example.com&test=test_call&prefix=teams.&w`

In the above example, you would have four separate monitors configured. My personal opinion is that you would only monitor the URL in **InstructionUri** as this is actually what users would access, but this is a personal preference.

## Conclusion
Yet another monitor type that can help to ensure Pexip Infinity is up and running for users. For non-Pexip scenarios you can surely see how useful this monitor type can be.

This is a Teams specific example and if your Infinity deployment is not integrated for Teams CVI then not much use. If you are thinking that you could use the HTTP(s) - Keyword monitor type for checking the Pexip Infinity webapp, you get points for thinking ahead, but there is an even better way to achieve, which will be explored in future posts.