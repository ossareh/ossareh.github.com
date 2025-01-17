---
layout: post
title: DRAFT
date: 2024-12-10 17:25:00 -0600
categories: networking
---

Using WinBox to configure a CAPsMAN network on mikrotik hardware.

Using a RB4011 series router, which will:

 - be a CAPsMAN host + internet router
 - wireless APs will pull config from CAPsMAN host
   - MikroTik cAP XL ac US Version (x5)
   - MikroTik hAP ax2 (x1); not in wifi mode (incompatible with cAP wifi, since there are 5 cAP they win)
 - wireless APs using PoE for power
 
GFiber router will be setup in bridge mode as per their instructions: https://support.google.com/fiber/answer/12091742?hl=en
 
Temporary changes you'll undo later:
 - tell ssh to not store ssh host fingerprint (-o UserKnownHostsFile=/dev/null); can be added as Host line in ~/.ssh/config
 - tell ssh to not prompt for fingerprint (-o StrictHostKeyChecking=no); can be added as a Host line in ~/.ssh/config
 
Configure ethernet on computer to address=192.168.88.10, no default route, no dns; this allows wifi to continue to be used for accessing documentation, etc.

Note that I was OK with 192.168.88.0/24 being my network, but some may not want that. In which case you'd need to change BOTH the DHCP configuration and the ip address assigned to the `bridge` interface

 Steps:
  1. reset to default settings
  2. connect using SSH (admin@192.168.88.1, password on the sticker on the router)
  3. change the admin username and password, configure SSH, disable services
  ```
  [admin@MikroTik]: user add name=d34db33f group=full
  password: **********
  [admin@MikroTik]: user remove admin
  [admin@MikroTik]: ip ssh set strong-crypto=yes
  [admin@MikroTik]: ip service set ssh port=2200
  [admin@MikroTik]: ip service disable telnet,ftp,www,api,api-ssl
  [admin@MikroTik]: ip dns set servers=8.8.8.8,1.1.1.1
  ```
  4. Reconnect with your new settings and then configure CAPsMAN
  ```
  [d34db33f@MikroTik]: caps-man configuration add name="Home Network" ssid="Genius Bar Stool" country="united states" datapath.bridge=bridge security.authentication-types=wpa2-psk security.passphrase="SuperComfort"
  [d34db33f@MikroTik]: caps-man provisioning add action=create-dynamic-enabled master-configuration="Home Network"
  [d34db33f@MikroTik]: caps-man manager set enabled=yes
  [d34db33f@MikroTik]: caps-man manager set upgrade-policy=suggest-same-version
  [d34db33f@MikroTik]:
  ```
  
  
  
Need to reword this:
A completely non-intuitive thing in the MikroTik world is that drivers (akin to kernel modules in Linux) enable various UI features. For example,
the RB4011 that I'm using as my border router supports both the old CAPsMAN and the new CAPsMAN. When using the UI to work things out, one must be
careful to use the WiFi->CAPsMAN menu not the Wireless->CAPsMAN menu. The latter is the old CAPsMAN and doesn't work with new devices. -_-

really important settings on the CAPsMAN
 - 5ghz frequency: 5240
 - bands (to only select 5ghz, do not support 2ghz)
 - Local Forwarding in datapath
