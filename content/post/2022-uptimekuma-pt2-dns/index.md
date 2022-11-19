---
title: "Monitoring Pexip Infinity with Uptime Kuma Part 2 - DNS Monitoring"
date: 2022-11-18T01:00:00+10:00
draft: false
tags: ["docker","monitoring","pexip","dns"]
---

In this part (2) of **Monitoring Pexip Infinity with Uptime Kuma** we will take a look at the DNS monitor type. If you have not yet read part one, [Monitoring Pexip Infinity with Uptime Kuma Part 1 - Introduction]({{< ref "/post/2022-uptimekuma-pt1/index.md" >}}), then go back and check that out first.

You are able to specify the hostname to check, the DNS record type that you are checking, and the DNS server to use for the lookup. This can be useful for [Pexip](https://www.pexip.com) Infinity as there needs to be DNS A records for all of the nodes and SRV records for each of the calling types being used on the platform. Therefore, Uptime Kuma allows the admin to monitor the existence of DNS records.

![Uptime Kuma DNS monitor overview](/post/2022-uptimekuma-pt2-dns/dns-monitor-overview.png#center "Uptime Kuma DNS monitor overview")

An important point to note is that this monitor type is a little bit different because *it will not indicate whether the host(s) served by the DNS record is online*. The monitor instead checks the existence of the DNS record only. Also, it unfortunately does not allow you to provide values for what the DNS look up result should actually be (would be a good feature to have).

For Infinity, you could monitor the DNS A records for all of the nodes in your deployment if you really wanted to, but it is probably overkill and unnecessary. You can effectively achieve the same thing by using a different monitor type for all of the nodes by using an FQDN as the hostname/URL value rather than IP, because the FQDN DNS record will have to be looked up anyway.

It is the various DNS SRV records which are typically created for Infinity is where this could be useful. It would be typical to have SRV records for the following services:

- **SIP**: `_sip._tcp.` and `_sips._tcp.`
- **H.323**: `_h323cs._tcp.` and `_h323ls._udp.` 
- **Skype for Business**: `_sipfederationtls._tcp.`
- **Pexip Infinity Connect Apps**: `_pexapp._tcp.`

## Adding a DNS SRV Record monitor
When adding a DNS monitor you provide the Hostname for the lookup, the DNS server to be used for the lookup via the Resolver Server field, and the record type. The remaining config options such as heartbeat settings and tags are the same as other monitor types.

![Uptime Kuma DNS monitor settings](/post/2022-uptimekuma-pt2-dns/dns-settings1.png#center "Uptime Kuma DNS monitor settings")

In the screenshot the entry is going to check the SRV record for SIP TLS on one of my lab domains: `_sips._tcp.aws.pexybeast.com` and will use the default resolver, which is Cloudflare. Many organizations will have internal and external nodes in their deployment served by internal and public DNS respectively, so you can monitor each set of records easily.

## Pexip DNS Checker
Pexip provides a handy online DNS SRV record checker at https://dns.pexip.com where you can enter your Pexip Infinity domain and it will perform a DNS lookup using public DNS and report back the results. It is useful for Uptime Kuma because it will display the values to add in the hostname field of the DNS monitor entry - work smarter, not harder!

![Pexip DNS Checker](/post/2022-uptimekuma-pt2-dns/dns-pexip-checker.png#center "Pexip DNS Checker")

## Conclusion
DNS monitoring can be useful as another data point to consider when you have problems with your calls and related services. It may be rare for DNS servers to go down, but it can happen, there was a recent example when Cloudflare DNS had an issue and it affected quite significant portion of internet services. What if a DNS admin accidentally deletes some of your Pexip SRV records? Ultimately, it's all about trying to get to the root cause of the problem as quickly as possible.