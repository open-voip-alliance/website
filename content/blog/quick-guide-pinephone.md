+++
author = "Jeroen"
avatar = "/images/element.png"
date = 2020-12-04T00:00:00Z
title = "Pinephone: Open ecosystem & hardware"
description = "How to use the Pinephone today"
images = ["/images/laptop-back.jpg"]

+++
_A guide to using the Pinephone_

## TLDR

I'm happily running Phosh on my Pinephone and use it as a news tablet &
Sonos player. As soon the last reachability & reliability issues are fixed, I
will also switch my SIM card back and finally say goodbye to my old Nokia.

## Background

Meet the [Pinephone](https://en.wikipedia.org/wiki/PinePhone); a mobile phone
that doesn't particularly stand out with its hardware & apps, when you
compare it with a typical Android or IOS device. In fact, the phone is not as
fast, the available mobile software is not very polished and it doesn't run most
of the popular mobile apps. But this is beside the point...

What makes the Pinephone particulary interesting is that it is a pragmatic open
hardware design. This makes it an excellent host for a Linux-based ecosystem.
Linux kernel support and mobile interfaces like [Phosh](https://developer.puri.sm/Librem5/Software_Reference/Environments/Phosh.html)
and [Plasma mobile](https://www.plasma-mobile.org/) are yet quite unstable;
the main reason why the Pinephone is primarily for Linux enthousiasts.

## Hardware update

In january 2020 I ordered a Pinephone Braveheart(1.1 rev. hardware). This was a
good excuse for me to get a bit [familiar]((https://gist.github.com/jvanveen/d395869f925605c09b0e5d1a0959b52b)
with [Plasma Mobile](https://www.plasma-mobile.org/) and [PostmarketOS](https://postmarketos.org/).
A couple of months later, I discoved that [Manjaro](https://forum.manjaro.org/tag/beta-release)
also offers recent ARM builds of Plasma mobile and [Phosh](https://developer.puri.sm/Librem5/Software_Reference/Environments/Phosh.html),
the mobile UI from the Purism [Librem 5](https://puri.sm/products/librem-5/) team.

I quite liked the Phosh UI when I first gave it a try. It feels simple, intuitive
and efficient. While trying Phosh, I also experienced some issues with my
particular Pinephone: the proximity sensor had an [issue](https://gitlab.manjaro.org/manjaro-arm/issues/pinephone/phosh/-/issues/10),
the [USB-C/HDMI](https://www.youtube.com/watch?v=j3jc7Mvn9Eo) output didn't work
and a recent kernel update [crashed](https://gitlab.manjaro.org/manjaro-arm/issues/pinephone/phosh/-/issues/104)
my phone.

Since the 1.1 hardware version, a lot of hardware issues have been [addressed](https://wiki.pine64.org/index.php/PinePhone_v1.2).
Two weeks ago, I ordered the new 1.2b hardware revision board(3GB/32GB). Recently,
I receive the upgraded board from Shenzen, China. [Replacing](https://wiki.pine64.org/index.php/PinePhone)
the old board is quite easy. All it takes is a fine-tip screwdriver and
some patience.

## Phosh

The EMMC of the new board is still empty. It has to be flashed with an operating
system of choice. I'm keeping an eye on Plasma mobile, but Phosh seems to be more
stable at the moment. So, lets give Manjaro Phosh beta 3 a try! A SD-card is
required for the installation:

```bash
# Download and flash Jumpdrive to the SD-card
wget https://github.com/dreemurrs-embedded/Jumpdrive/releases/download/0.6/pine64-pinephone.img.xz
xz -d pine64-pinephone.img.xz
sudo dd if=pine64-pinephone.img of=/dev/mmcblk0 bs=4M

# Download the Phosh image:
wget https://osdn.net/projects/manjaro-arm/storage/pinephone/phosh/beta3/Manjaro-ARM-phosh-pinephone-beta3.img.xz
xz -d Manjaro-ARM-phosh-pinephone-beta3.img.xz
```

Insert the SD-Card in the Pinephone and boot the Jumpdrive tool. Connect the phone
with the USB-C cable to a computer. Find out the EMMC drive's name that's being
exposed by Jumpdrive. In my case the EMMC is **/dev/sda**.

_Make sure that you identify the right partition before running
the **dd** command. It is called Disk Destroyer for a reason..._

```bash
lsblk
# (!) verify the EMMC partition name first (/dev/sda)
sudo dd if=Manjaro-ARM-phosh-pinephone-beta3.img of=/dev/sda bs=4M
```

After the **dd** tool completed, shutdown the Pinephone, remove the SD-Card and
insert a SIM-card. Power up the phone and boot Phosh. Enter the SIM-lock PIN.
Unlock the phone by swiping up and entering te default password: *123456*.

And...Bob is your uncle! We just installed Phosh on the Pinephone. Step through
the wizard and connect to a Wifi network. Find out the phone's ip address and
login to the phone from a computer on the same network:

```bash
# From the phone:
ip a
# From your computer:
ssh manjaro@<phone-lan-ip>  # Use default password
```

Lets setup mobile data. In this example, I'm using the settings from a
dutch provider called *Simpel*. Your own provider should have these APN
settings listed on their website:

```bash
sudo mmcli -m 0 --create-bearer='apn=internet.access.nl,allowed-auth=chap,allow-roaming=no'
sudo nmcli c add type gsm ifname cdc-wdm0 con-name SIMPEL apn internet.access.nl
ip a
```

There is a **wwan0** interface with an assigned ip. You can verify the data
connection by closing wifi and pinging a domain from the phone's terminal; e.g.

```bash
ping openvoipalliance.org
```

## Daily Driver check

To be honest; I find this the most boring part of reviewing the phone. Yes,
I would like to use it as my daily mobile phone. My current phone doesn't do much
besides calling, sms and running a week on one battery charge. Anyway, I've used
the Pinephone for two days as my mobile phone and here are my findings:

### Reachability - contacts, calls & sms

I would like to have my contacts stored outside of the phone, so a phone
crash won't wipe them all. I installed [Nextcloud](https://wiki.archlinux.org/index.php/Nextcloud)
on a VPN and used the Nextcloud Contacts sync option in Gnome Contacts to sync
contacts from my Nextcloud to the phone. I was quite excited about this
functionality and explored Nextcloud a bit.

Both incoming and outgoing calls work well. Incoming calls set off a ringtone
and the audio quality and latency was quite alright. However, the phone enters
a deep sleep mode after 5 minutes. Waking up from this mode doesn't seem to work
properly yet; it missed several incoming calls during the two days. My phone
provider sends a SMS after a missed phone call, but I need to be sure that
people are able to reach me on this phone. That is the main reason why I
switched my SIM back to the old Nokia for now.

Incoming and outgoing SMS works fine in the chat app. However, there is no
notification for new messages, so I have to manually open the chat application.
Then there is also a Telegram client pre-installed, but its something I
wouldn't use for the same reason. The pre-installed Fractal Matrix client works
quite nice, besides a quirk that prevents you to enter your username. Personally,
I'm happy with only SMS functionality for now.

### Reliability

With Phosh, I can keep the phone idle for about a day on one charge. While
using it intensively, it would maybe last a couple of hours. Then there is
some news about the [charging safety](https://xnux.eu/log/#017) of
the Pinephone, which is the reason why it will only charge to 89% capacity
for now. The phone is not getting too hot, even when using it in convergence
mode, so I'm not that worried about it. However, its good that see some
awareness of the issue, before accidents happen. After a while, when
unlocking the phone, the touch input seems to be lagging one event behind
all the time, which forces me to reboot the phone.

## Pocket-size Computer

![Example image](/images/blog/convergence.jpeg)

This is what I get really exciting about. Yes, the phone part is not 100% there
yet, but it will in time, considering the enormous amount of work that
has already been done by the community. The interesting part of this device is
that it is actually a full-blown pocket-size Linux computer, able to run most of
the software that also runs on a desktop computer.

It runs the default Firefox desktop browser with some mobile tweaks.
The navigation bar is still somewhat quirky, but sites like [Hacker news](https://news.ycombinator.com/)
load fine and scrolling feels responsive. The browser plays videos without
breaking a sweat. Overall, this is a fine responsive browser, which makes it
already a replacement for my casual news-checking tablet. A small tip to
improve performance is to enable the webrender engine:

```bash
about:config
gfx.webrender.all True
```

Connecting a HDMI monitor with a USB-C dock worked quite good. The phone
switches to docked mode and the extra screen space is handy. Connecting both a
Bluetooth mouse and Bluetooth keyboard worked out of the box. The first thing I
did was to install some basic tooling:

```bash
sudo pacman -S base-devel rust go node.js fakeroot yay vim git grim
```

I don't have a local music collection (anymore) and never looked back since I'm
using Spotify. There is no dedicated Spotify GUI yet for Phosh, but there is an
interesting open-source commandline Spotify client [ncspot](https://github.com/hrkfdn/ncspot).
Since we got Rust and basic build tools installed, why not try to compile ncspot?
Streaming music to a Sonos device can be done with [Noson](https://github.com/janbar/noson-app).
It also includes the Sonos version of a Spotify GUI. To install both:

```bash
yay install ncspot noson-app
```

![Spotify player](/images/blog/ncspot.png)
![Sonos Controller](/images/blog/noson.png)

Having access to a wide range of open-source communities, tool-chains
and software is a powerful thing that enables a lot of capabilities.
Overall, I find this a refreshing device, which is already useful as
a tablet/streaming device. I don't doubt that it will soon replace my
old phone. Definitely worth the try!
