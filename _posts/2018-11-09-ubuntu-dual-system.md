---
title: Ubuntu 18.04 Dual System Install Disk Solution
date: 2018-11-12
description: Note down my disk management on alienware with Ubuntu 18.04 dual system.
categories:
- Linux
tags:
- Linux
photos:
-
---

# Base

## System

Already installed _Windows 10_ with UEFI booting option.

## Disks

- 1 256G SSD: PM951 NVMe SAMSUNG (GUID Partition Table)
  + 160G `C:` for _Windows_
  + _Free space_
- 1 1T Hard Disk: HGST HTS721010A9E630 (GUID Partition Table)
  + 523M Recovery partition for _Windows_
  + 105M EFI booting system for _Windows_
  + 256G `D:` for _Windows_
  + 256G `E:` for _Windows_
  + _Free space_

# For Ubuntu 18.04

Add the following partitions:

- SSD, 500M, Logical Partition, EFI Format (boot)
- SSD, 16G, Primary Partition, Swap Space (= physical memory)
- SSD, 40G, Primary Partition, EXT4 Format, Mount on '/' (root)
- HD, 400G, Primary Partition, EXT4 Format, Mount on '/home' (home)

> Remember to install grub on the same partition as EFI.

# Final Snapshot of Disk

- 1 256G SSD: PM951 NVMe SAMSUNG (GUID Partition Table)
  + 160G `C:` for _Windows_
  + 500M EFI booting for _Ubuntu_
  + 16G Swap space for _Ubuntu_
  + 40G '/' for _Ubuntu_
  + _Free space_
- 1 1T Hard Disk: HGST HTS721010A9E630 (GUID Partition Table)
  + 523M Recovery partition for _Windows_
  + 105M EFI booting system for _Windows_
  + 256G `D:` for _Windows_
  + 256G `E:` for _Windows_
  + 400G '/home' for _Ubuntu_
  + _Free space_

# Reference

- [GPT+UEFI+双硬盘+双系统 win10+ubuntu 安装指导](https://blog.csdn.net/sizaif/article/details/79399130)
- [ubuntu 18.04/16.04/14.04 双硬盘分区方案](https://blog.csdn.net/u010801439/article/details/80485251)