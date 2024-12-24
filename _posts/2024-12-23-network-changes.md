---
layout: post
title: Changes to Our Home Network
date: 2024-12-23 22:55:00 -0600
categories: networking
---

Our home is a not-so-large canyon that results in terrible cell phone signal on our ground floor and
pretty terrible signal on our upper floor. As such we heavily rely on WiFi calling. Additionally our
home is wide and tall but not so deep and seemingly made from various WiFi-signal nixing materials.
Until recently I've made do with three [Deco M5] APs in infrastructure mode using our COAX cable as
the backhaul to a 1gbps GFiber connection. In all this as run pretty well. The Deco APs do not have
a COAX themselves so I bridged the ethernet<->COAX using a Motorola [MM1000] MoCa adapater.

[Deco M5]: https://www.amazon.com/TP-Link-Deco-Whole-Home-System/dp/B06WVCB862
[MM1000]: https://www.amazon.com/gp/product/B077Y3SQXR/ref=ppx_yo_dt_b_search_asin_title

Recently I decided it would be good to move over to ceiling mounted WiFi routers to ensure better
quality coverage. The give away to us that WiFi wasn't cutting it was that participants on WiFi
calls would ask us to repeat ourselves somewhat frequently; if we moved into areas with better
coverage the request for repetitions would go down. Obviously this is far from scientific but after
5-odd years we felt pretty sure the system wasn't doing so well. The most acute example of an issue
was while watching TV, we'd often get derezzed - presumably because our TV was in an even weaker
spot for WiFi signal reception.

I opted to go with [MikroTik cAP XL ac] ceiling routers back hauled over PoE CAT6 to a [MikroTik
CRS328-24] switch which in turn is connected over 10G SFP+ to a [MikroTik RB4011] router. The APs,
switch and router have 1gbps ports on them, so the back haul is pretty wildly over-provisioned. We
run our GFiber router in bridge mode, it has a 10G port for this purpose.

[MikroTik cAP XL ac]: https://www.amazon.com/dp/B09H2CCVZX?ref=ppx_yo2ov_dt_b_fed_asin_title
[MikroTik CRS328-24]: https://www.amazon.com/dp/B07C657P7Q?ref=ppx_yo2ov_dt_b_fed_asin_title
[MikroTik RB4011]: https://www.amazon.com/dp/B07HBW2NTR?ref=ppx_yo2ov_dt_b_fed_asin_title

We installed five AP's to replace our prior three. We also ran a PoE ethernet line to the bookcase
our TV is in and hooked it upto a [MikroTik hAP ax2]; i.e. directly into the 1gbps.

[MikroTik hAP ax2]: https://www.amazon.com/dp/B0BRSCGFPN?ref=ppx_yo2ov_dt_b_fed_asin_title

Prior to switching over our network I ran some pretty non-scientific tests to build a sense of
whether the cost and effort was worth it. The end result our WiFi devices now get approx. 10x better
throughput (30mbps vs 300mbps). Updating to the WiFi6 (ax) line of MikroTik APs should get us up
closer to 1gbps for our WiFi devices but isn't really worth the extra hardware outlay and $360/yr in
ISP charges.

This change has resulted in our desks no longer having hardwired connections, taking us from 1gbps
-> 300mbps - so in the somewhat near term I'll need to fix that. (tl;dr; I moved the GFiber line
into our utility room, away from our closet where all of our COAX terminated).

This test does not mean to disparage the Deco line of routers; they are ~6 years old and likely do
not support the ac WiFi standard. The reasons I chose to make this change were due to:

1.  I grew up running a small home LAN, I like this stuff!, and
2.  MikroTik does not push products that require corporate integrations like TP-Link does.

Sure, it is nice to be able to manage your network kit from a mobile phone (assuming your cell
signal is good), but the hacks and outages at folks like [SolarWinds], Okta, Crowdstrike/Microsoft
has made me incredibly skeptical of the centralized control methodology. If it can happen to
[Ubiquiti] it can happen to TP-Link.

[SolarWinds]:
  https://www.techtarget.com/whatis/feature/SolarWinds-hack-explained-Everything-you-need-to-know
[Okta]: https://www.twingate.com/blog/tips/okta-data-breach
[Crowdstrike/Microsoft]: https://en.wikipedia.org/wiki/2024_CrowdStrike-related_IT_outages
[Ubiquiti]: https://www.twingate.com/blog/tips/ubiquiti-data-breach

## Test Details

Please remember, this is non scientific!

The tests I ran on the old kit:

- sit next to access point, perform speed test
- wire laptop into access point, peform speed test
- if applicable, wire laptop into MoCa adapter, perform speed test
- wire into switch in front of GFiber router, perform speed test
- wire into GFiber router, perform speed test

On the new kit:

- power on one AP at a time, sit near it, connect to it, run speed test

On the old kit once connected over ethernet everything was 1gbps. Which really highlights just how
viable a solution MoCa adapters are for homes. If it wasn't for wanting ceiling APs to reduce on
wire clutter I'd have just upgraded the WiFi APs and been cool with it. One thing I did notice is
that latency spiked when the MoCa was in the loop. For when wired over pure ethernet to our GFiber
router ping times were consistently around 8ms to 1.1.1.1 and 13ms when a MoCa was in the loop. With
the new network kit I see pings around 8-11ms.

I chose not to power off the various Deco APs during the tests because most of our home connects to
them, so I didn't want devices switching between routers to screw with the data. Honestly the data
is not that interesting, suffice it to say: WiFi was slower than wires maxing out at around 40mbps
at best. Once wires were involved we were at 1gbps. The new APs come in at 300-350mbps.

ChatGPT says this is a good test:

```
$ brew install speedtest-cli
$ speedtest-cli --secure --single --json --bytes --server 23449
```

Server `23449` doesn't appear to exist anymore; it was reported at being Texas A&M.
