---
title: Raspberry Pi 3 - Raspbian Configuration
date: 2019-08-08
description: This notes some configuration processes with Raspbian on Raspberry Pi 3.
categories:
- RaspberryPi
tags:
- RaspberryPi
photos:
---

## Intro

I've been playing with Raspberry Pi for a while, but always with NOOBS. So this time I tried with Raspbian and I would like to take some notes about how I configured it.

## First Boot

To begin with, download Raspbian from [here](https://www.raspberrypi.org/downloads/), I chose Lite (around 400MB). As usual, I then burn the image to both my SD card and/or USB key with Etcher, which is for free but can be tricky for my SD card (I'll talk about it later).

### SSH

Since I would like to avoid using either mouse or keyboard, it's quite important for me to configure SSH before hand. Raspi will check in the boot directory (which is our SD card / USB root directory) whether there is a file called `ssh`. If so, the SSH interface will be automatically turned on without using classy `raspi-config`. So just `cd` to the root directory of SD card and USB and `touch ssh`.

### Wi-Fi or Ethernet?

#### Wi-Fi

It'll always be great if we could avoid using Ethernet cable (it's just so inconvenient to search for the cable... Sorry I'm a bit lazy). It seems that we could just create a file called `wpa_supplicant.conf` and put all network configuration in it:

```bash
sudo tee ./wpa_supplicant.conf &>/dev/null << EOF
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
	ssid="your_ssid"
	psk="your_password"
	priority=4
    proto=RSN
    key_mgmt=WPA-PSK
    pairwise=CCMP
    auth_alg=OPEN
}
EOF
```

which will be later put in `/etc/wpa_supplicant/wpa_supplicant.conf`.

After booting our Raspberry pi for the first time, we should be able to find its ip address via `arp -a` or in your router dashboard, hence we could then SSH to it.

#### Ethernet

Just in case it doesn't work for you, unfortunately, we have to look for a cable... After plugging it to your router, similarly, we should also be able to find its ip address. Then we could put something like:

```bash
sudo tee /etc/network/interfaces.d/interfaces &>/dev/null << EOF
auto lo
iface lo inet loopback

#auto eth0
allow-hotplug eth0
iface eth0 inet dhcp

auto wlan0
allow-hotplug wlan0
iface wlan0 inet dhcp
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
EOF
```

which basically tells that we should go check `wpa_supplicant.conf` for wifi ssid and password information.

Then we put the same thing as is shown above in `/etc/wpa_supplicant/wpa_supplicant.conf`. Then we can reboot and unplug the Ethernet cable. Now it should work.

#### Static IP address

As is mentioned [before](https://zhiyuanyaoj.github.io/raspberrypi/2018/01/02/raspi-wifi-ssh/), in order to have static IP address on your Raspberry Pi, just put something like below in `/etc/dhcpcd.conf`.

```
interface eth0
static ip_address=10.1.10.100/24
static routers=10.1.10.1
static domain_name_servers=10.1.10.1

interface wlan0
static ip_address=10.1.10.101/24
static routers=10.1.10.1
static domain_name_servers=10.1.10.1 
```

Replace these IP addresses with your only IP, _e.g._ ip_address can be `192.168.1.42`, router and DNS can be `192.168.1.1`.

#### Some interesting observations

If we put:

```
auto lo
iface lo inet loopback

#auto eth0
allow-hotplug eth0
iface eth0 inet dhcp

auto wlan0
allow-hotplug wlan0
iface wlan0 inet dhcp
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```

in `/etc/network/interfaces`, static IP won't work and we will see some error messages like `failed to start dhcpcd on all interfaces` and its log like:

```
dhcpcd[399]: Not running dhcpcd because /etc/network/interfaces
dhcpcd[399]: defines some interfaces that will use a
dhcpcd[399]: DHCP client or static address
```

I think it's because the kernel looks for `dhcp`-like keywords in interfaces which blocks dhcp client deamon from working. But just by putting the same configuration in `/etc/network/interfaces.d/interfaces` and remains the original `/etc/network/interfaces` as:

```
# interfaces(5) file used by ifup(8) and ifdown(8)

# Please note that this file is written to be used with dhcpcd
# For static IP, consult /etc/dhcpcd.conf and 'man dhcpcd.conf'

# Include files from /etc/network/interfaces.d:
source-directory /etc/network/interfaces.d
```

will fix this hiccup.


## SD Card or USB?

Since I only have one SD card currently which is supposed to be used for my DJI camera, I would like to use my USB key as boot for Raspi. In order to do that, I followed [this blog](https://www.stewright.me/2013/05/install-and-run-raspbian-from-a-usb-flash-drive/) a bit. But this one is a little bit outdated. 

What we need to do is to duplicate all the files in our SD card and put it in the USB. Then in the SD card, modify `config.txt` and add `program_usb_boot_mode=1`, which will change OTP(One Time Programmable) setting for good. Then if we pop both SD card and USB in Raspberry Pi, it will switch on its `USB Boot Mode`. We can verify by running:

```bash
$ vcgencmd otp_dump | grep 17:
17:3020000a
```

If wee see output like above, it means we have succeeded.

Now we can boot Raspi with only USB plugged in. However, it still will first look for whether there is an SD card or not, so it will induce 5-10s latency. 

## Locale Configuration

Sometimes we see perl warnings like:

```
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LANG = "en_US.utf8"
    are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").
```

which can be solved with:

```bash
export LANGUAGE=en_US.UTF-8
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
locale-gen en_US.UTF-8
dpkg-reconfigure locales
```

## Make SSH Easier and Safer

### Easier

I felt pretty exhausted times after times typing `ssh pi@xxx.xxx.xxx.xxx`. Hence, I decided to create my own **alias** in `~/.zshrc` (BTW. I am using zsh):

```
# SSH connect to Raspberry Pi through default Wi-Fi address
alias sshpi="ssh pi@192.168.1.50"
```

By doing this, whenever I want to connect to RPi via SSH, I could just type `$ sshpi` in my terminal.

### Safer

Even though I can save my time from typing `ssh pi@xxx.xxx.xxx.xxx`, I still need to type my password afterwards. This procedure can be not only troublesome but also dangerous in some cases. So I decided to encapsulate my password in my laptop.

### Check for existing SSH keys

First, check whether there are already keys on the computer you are using to connect to the Raspberry Pi:

```shell
$ ls ~/.ssh
```

If you see files named `id_rsa.pub` or `id_dsa.pub` you have keys set up already, so you can skip the generating keys step (or delete these files with `rm id*` and make new keys).

#### Generate new SSH keys

To generate new SSH keys enter the following command (Choose a sensible hostname such as `<YOURNANME>@<YOURDEVICE>` where we have used `eben@pi`):

```shell
$ ssh-keygen -t rsa -C eben@pi
```

You can also use a more descriptive comment using quotes if you have spaces, e.g. `ssh-keygen -t rsa -C "Raspberry Pi #123"`

Upon entering this command, you'll be asked where to save the key. We suggest you save it in the default location (`/home/pi/.ssh/id_rsa`) by just hitting `Enter`.

You'll also be asked to enter a passphrase. This is extra security which will make the key unusable without your passphrase, so if someone else copied your key, they could not impersonate you to gain access. If you choose to use a passphrase, type it here and press Enter, then type it again when prompted. Leave the field empty for no passphrase.

Now you should see the files `id_rsa` and `id_rsa.pub` in your `.ssh` directory in your home folder:

```shell
$ ls ~/.ssh
authorized_keys  id_rsa  id_rsa.pub  known_hosts
```

The `id_rsa` file is your private key. Keep this on your computer.

The `id_rsa.pub` file is your public key. This is what you put on machines you want to connect to. When the machine you try to connect to matches up your public and private key, it will allow you to connect.

Take a look at your public key to see what it looks like:

```shell
$ cat ~/.ssh/id_rsa.pub
```

It should be in the form:

```
ssh-rsa <REALLY LONG STRING OF RANDOM CHARACTERS> eben@pi
```

Copy your public key to your Raspberry Pi

If your Pi does not have an `.ssh` directory you will need to set one up so that you can copy the key from your computer.

```shell
$ cd ~
$ install -d -m 700 ~/.ssh
```

To copy your public key to your Raspberry Pi, use the following command to append the public key to your `authorized_keys` file on the Pi, sending it over SSH:

```shell
$ cat ~/.ssh/id_rsa.pub | ssh <USERNAME>@<IP-ADDRESS> 'cat >> .ssh/authorized_keys'
```

Note that this time you will have to authenticate with your password.

Now try `ssh <USER>@<IP-ADDRESS>` or your own alias (in my case `sshpi`) and you should connect without a password prompt.

## Conclusion

Cheers! :)

## Ref

- [[1] Install and run Raspbian from a USB Flash Drive](https://www.stewright.me/2013/05/install-and-run-raspbian-from-a-usb-flash-drive/)
- [[2] 树莓派3B—完全u盘启动系统](https://www.jianshu.com/p/2ed5f1c6367b)
- [[3] Handle confliction between dhcpcd and /etc/network/interfaces](https://www.raspberrypi.org/forums/viewtopic.php?t=191678)
- [[4] 树莓派如何完全无头(无屏无网线无键盘鼠标)安装](https://www.jianshu.com/p/f260967aefb1)
- [[5] Perl warning Setting locale failed in Debian](https://www.thomas-krenn.com/en/wiki/Perl_warning_Setting_locale_failed_in_Debian)