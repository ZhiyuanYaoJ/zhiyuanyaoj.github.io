---
title: Raspi - Setup for video streaming
date: 2019-04-06
description: Setup my raspi which will serve as a video streaming (and probably real-time processing) device.
categories:
- RaspberryPi
tags:
- RaspberryPi
photos:
-
---

# VNC Server

I'm using macbook, so [VNCViewer](https://www.realvnc.com/en/connect/download/viewer/macos/) is good for me. Finger crossed for windows/linux users. :)

Then to configure Raspi, simply follow [this instruction](https://www.raspberrypi.org/documentation/remote-access/vnc/):

```bash
sudo apt-get update
sudo apt-get install realvnc-vnc-server realvnc-vnc-viewer
```

and then configure via `sudo raspi-config` -> *Interface Option* -> *VNC* -> *Enable*.

Finally, open the VNCViewer or whatever app as an interface, enter the host ip address (_e.g._ for me it's `192.168.1.33`) and it's done!