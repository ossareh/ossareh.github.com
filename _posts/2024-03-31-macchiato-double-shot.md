---
layout: post
title: Macchiato Double Shot
date: 2024-03-07 20:20:00 -0600
categories: homelab
---

Four years ago I bought a [Macchiato DoubleShot][board-docs] from SolidRun. It's a quad core ARM64
board which SolidRun seems to have disowned somewhat. The only reference on their marketing site is
to ["connect it to GCP"][board-gcp]... but nothing about how, if you click the "buy now" button,
you're sent to a product page with many products **but not** the Double Shot. Derp.

At the time I could not work out how to get an OS on it. SolidRun themselves had a hacked phpBB
which contained only spam, and search terms for the board were poorly indexed. The box the board
came in has #27 written on it... I don't know if that means I have the 27th manufactured board.. or
bought board. Since then the [docs][board-docs] have improved enough to a point where I could fumble
my way through it. Interestingly the [docs][board-docs] have images of dip switches on the board
which are jumper pins on my board. So there was a rev at some point; presumably after I bought it.

I've decided to run a little single node kubernetes cluster on this board, mainly because I want to
self host a few services locally. I aim to put them on a home [tailnet][tailscale-tailnet] so that
when we travel we can maintain access to them despite being on some arbitrary network. This board
will be running in my daughters temporary bedroom, and I'd like it to not disturb her. She likes
white noise, so I'm OK with something in the low decibel region. The board itself was is very loud:

<iframe width="315" height="560"
  src="https://youtube.com/embed/OzQym94xg2g"
  title="YouTube video player"
  frameborder="0"
  allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

To get this into my home mini-rack I bought a 1U short case, some much quieter fans from Noctua, and
some SSDs. Full list here:

- 1 x [IN-WIN IW-RF100S-S265 1U Short Mini-ITX w/ 265W PSU](https://www.amazon.com/dp/B075FPS6ZL)
- 3 x [NF-A4x20 PWM](https://www.amazon.com/dp/B071W93333) case fans
- 1 x [NF-A4x10 PWM](https://www.newegg.com/noctua-nf-a4x10-pwm-case-fan/p/1YF-000T-000N3) CPU fan
- 3 x [Samsung EVO 870 SATA 3 2.5in SSDs](https://www.amazon.com/dp/B08QB93S6R)
- 2 x [Sabrent 2.5->3.5in drive caddies](https://www.amazon.com/dp/B00G57BN1M)
- 3 x [SATA 3 cables](https://www.amazon.com/dp/B07JFQ2H9R)
- 1 x [PWM fan header splitter](https://www.amazon.com/dp/B07M5P7VHG)

The CPU Fan took a while to come in, so beside that this is what it looks like packaged up:

![Packaged board](/assets/homelab-packaged.png).

I'm skeptical that the case fans will work well; SolidRun decided to put the RAM slot on the
opposing side of the IO expansion, so as far as I can reason there's never a way the airflow would
not be blocked by the RAM.

There is only one fan header so the splitter came in useful. The Noctua's have a really low amperage
(0.05A, IIRC), the fan included with the board draws 0.88A, I didn't take note of what the shipped
case fans were. The low amperage means I could easily run 4 fans off the one header, which is rated
at 1A.

To get an OS on the board I followed these steps:

1. Connect laptop to on-board serial console
1. Update U-Boot on on-board SPI to latest U-Boot
1. Configure to boot from on-board eMMC
1. Install Debian to eMMC

The on-board serial port is microUSB; `screen /dev/cu.usbserial-DT03V290 115200` connects you to it.

U-Boot shipped by default on the on-board SPI; U-Boot has various commands for loading binaries from
sdcard / USB. I formatted an sdcard as FAT16 (`sudo newfs_msdos -F 16 /dev/disk4`; I worked out
which disk to reference using `diskutil list`) and put the U-Boot binary on it. I then read the blob
into memory, and issued the correct flashing command. The
[steps are here](https://github.com/SolidRun/Documentation/blob/bsp/8040/u-boot.md#to-spi-flash) and
vary based on what medium you're using.

Rebooting with the new U-Boot changed the boot process, you interupt it with `esc` and then you get
a graphical UI. Pretty cool over serial, honestly. That UI can be used to set boot devices; I set it
to USB and inserted a debian installation USB stick. Rebooted, and installed debian to the on-board
eMMC. From there you're home free.

[board-docs]: https://solidrun.atlassian.net/wiki/x/BQUWEQ "Documentation for the board"
[board-gcp]: https://www.solid-run.com/macchiatobin-gcp/ "Connect to Google’s GCP with MACCHIATObin"
[tailscale-tailnet]: https://tailscale.com/kb/1136/tailnet "What is a tailnet?"
