---
title: "Port Forwarding HTTPS on ASUS router"
date: 2022-10-06T16:10:02+10:00
draft: false
tags: ["asus","https","port forwarding"]
---

Recently I was struggling to port forward HTTPS (TCP 443) on my internet router and spent some time trying to figure this out. I’m sharing what I found in case you may have run into the same issue. This is specific to ASUS routers. 

My goal was to expose a [NGINX Proxy Manager](https://nginxproxymanager.com/) container to allow me remote access to video systems on my internal network for when I am working remotely. It all seemed to be fine, I just could not get HTTPS exposed externally. 

## First things first

You not only need to think about your router but also some other aspects of your connection to successfully be able to port forward HTTPS into your home network: 

1. **Does your ISP support forwarding of the HTTPS port?** If so, do you need to make a support request for this to be enabled? My ISP supports it if you have a static IP address and was lucky enough that it could be enabled using their portal. 

2. **Is HTTPS open on the target device?** May be obvious to some, ensure host you are trying to expose allows connections on TCP 443. 

Without these points covered then you are not going to be able to get it to work regardless of your router config. 

## ASUS Config 

The router was allowing me to add TCP 443 as an entry in the port forwarding section yet would not work still – frustrating!

After some searching I tried one of the suggestions I found online which fixed my issue. My router had the AI Cloud feature included, however, it was not enabled on my device. You will find on one of the config pages that feature is configured to use 443 – this was my problem, a clash even though the feature was not enabled. 

To fix, I changed the AICloud Web Access Port from 443 to something else, I used 9443. After saving my config, it worked like a charm!

![ASUS AI Cloud Config](/post/2022-asus-https/ai-cloud1.png#center "Menu options")