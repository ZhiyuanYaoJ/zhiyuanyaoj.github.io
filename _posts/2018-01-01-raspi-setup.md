---
title: Raspberry Pi 3 - Setup
date: 2018-01-01
description: Finally setting up my Raspberry Pi 3 after having it for half a year.
categories:
- RaspberryPi
tags:
- RaspberryPi
photos:
- https://ericyaoj.github.io/blog/assets/images/raspi/raspberry-pi-cover.png
---

## Intro

Having bought my raspberry pi 3 for almost half a year, I finally installed OS on it. Though I have to say that the main reason for me to do it is because I felt terribly boring on the very first day of 2018. So in the following I am going to take a note of all the procedures I took to setup my Raspberry Pi 3.

## Install OS

### What we need?

- **Raspberry Pi 3**
- **SD card** (more than *16GB* recommended)
- **Screen**
- **Keyboard**
- **Ethernet cable**
- **Working router**
- **Power supply** (5V recommended)
- **Internet Connection!**

### Procedures

![Download Page](https://ericyaoj.github.io/blog/assets/images/raspi/download-page.png)

1. Go to [download page](https://www.raspberrypi.org/downloads/) of [raspberrypi.org](https://www.raspberrypi.org/)
2. Download _NOOBS_ as `zip` file
3. Format our SD card
4. Unzip `NOOBS_v_xxx.zip` directly to the formatted SD card
5. Insert the SD card to Raspberry Pi 3 (there is an interface embedded in it)
6. Connect Raspberry Pi 3 to a screen, a keyboard, an Ethernet cable that connect directly to our router, and most importantly, a compatible power supply
7. Now we are all set and good to go! :)
8. Switch on our Raspberry Pi 3 now and waiting for it to boot itself
9. Follow the guidance from the GUI
10. Done! :)

## Connect to Wi-Fi

I feel pretty sorry when I put _Ethernet cable_ on the list above. It took me like half an hour to find mine. Meanwhile, I found it really bothering having one more disturbing wire on top of my table which has already been messy enough.

So, let's get rid of the wire and come to a wireless world!

### Setting up Wi-Fi connection

1. Open the terminal (keyboard shortcut `ctrl+alt+T`)
2. Edit the network interfaces file (`$ sudo nano /etc/network/interfaces`)
3. Replace whatever in the file to:

```
auto wlan0

allow-hotplug wlan0
iface wlan0 inet dhcp
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp
```

> Note:
> - This file `/etc/network/interfaces` contains all known network interfaces.
> - The bottom four lines allows wlan as a network connection method, and use `/etc/wpa_supplicant/wpa_supplicant.conf` as our configuration file, which actually is also true without explicit configuration like this.

4. Quit and save (`ctrl+X`, then `Y`, then `enter`)

### Configuring Wi-Fi connection

We can do it in many different ways, like from GUI interface, or typing `$ sudo raspi-config` in the terminal. Here, I am taking note of what is actually happening in our OS.

1. Open the `wpa_supplicant.conf` file (`$ sudo nano /etc/wpa_supplicant/wpa_supplicant.conf`)
2. Add the information of your Wi-Fi:

```
network={
    ssid="YOUR_NETWORK_NAME"
    psk="YOUR_NETWORK_PASSWORD"
    proto=RSN
    key_mgmt=WPA-PSK
    pairwise=CCMP
    auth_alg=OPEN
}
```

> Note:
> - Basically what we need are `ssid` and `psk`
> - In case you are interested in the rest four settings, take a look at the [ref[1]](http://weworkweplay.com/play/automatically-connect-a-raspberry-pi-to-a-wifi-network/)

3. Quit and save (`ctrl+X`, then `Y`, then `enter`)

Now our Raspberry Pi should be all set. Reboot it and see if it works.

## Conclusion

Now I have basically installed OS on my Raspberry Pi and got rid of the annoying Ethernet cable. I will keep calm and play with RPi. Cheers!

## Ref

[[1] Automatically connect a Raspberry Pi to a Wifi network](http://weworkweplay.com/play/automatically-connect-a-raspberry-pi-to-a-wifi-network/)