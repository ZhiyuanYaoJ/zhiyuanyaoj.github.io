---
title: VPP Proxy Error
date: 2019-02-12
description: A solution to an error I came across recently when execute make build.
categories:
- Linux
- VPP
tags:
- Linux
- VPP
photos:
-
---

# Error

```bash
root in vpp on master took 2s
$ make build
make[1]: Entering directory '/home/yzy/tmp/vpp/build-root'
@@@@ Arch for platform 'vpp' is native @@@@
@@@@ Finding source for external @@@@
@@@@ Makefile fragment found in /home/yzy/tmp/vpp/build-data/packages/external.mk @@@@
@@@@ Source found in /home/yzy/tmp/vpp/build @@@@
@@@@ Arch for platform 'vpp' is native @@@@
@@@@ Finding source for vpp @@@@
@@@@ Makefile fragment found in /home/yzy/tmp/vpp/build-data/packages/vpp.mk @@@@
@@@@ Source found in /home/yzy/tmp/vpp/src @@@@
@@@@ Configuring external: nothing to do @@@@
@@@@ Building external in /home/yzy/tmp/vpp/build-root/build-vpp_debug-native/external @@@@
make[2]: Entering directory '/home/yzy/tmp/vpp/build/external'
==========================================================
Building DPDK from source. Consider installing development
package by invoking 'make install-ext-deps' from the
top level directory
==========================================================
make config
make[3]: Entering directory '/home/yzy/tmp/vpp/build/external'
make[3]: warning: jobserver unavailable: using -j1.  Add '+' to parent make rule.
--- extracting dpdk-19.02.tar.xz ---
--- generating custom config from /home/yzy/tmp/vpp/build-root/build-vpp_debug-native/external/dpdk-19.02/config/defconfig_x86_64-native-linuxapp-gcc ---
make[4]: Entering directory '/home/yzy/tmp/vpp/build-root/build-vpp_debug-native/external/dpdk-19.02'
Configuration done using custom-config
make[4]: Leaving directory '/home/yzy/tmp/vpp/build-root/build-vpp_debug-native/external/dpdk-19.02'
make[3]: Leaving directory '/home/yzy/tmp/vpp/build/external'
make[2]: Leaving directory '/home/yzy/tmp/vpp/build/external'
@@@@ Installing external @@@@
make[2]: Entering directory '/home/yzy/tmp/vpp/build/external'
make install
make[3]: Entering directory '/home/yzy/tmp/vpp/build/external'
mkdir -p downloads
Downloading http://www.nasm.us/pub/nasm/releasebuilds/2.13.03/nasm-2.13.03.tar.xz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0curl: (7) Failed to connect to www.nasm.us port 80: Connection refused
packages/nasm.mk:21: recipe for target 'downloads/nasm-2.13.03.tar.xz' failed
make[3]: *** [downloads/nasm-2.13.03.tar.xz] Error 7
make[3]: Leaving directory '/home/yzy/tmp/vpp/build/external'
Makefile:159: recipe for target 'ebuild-install' failed
make[2]: *** [ebuild-install] Error 2
make[2]: Leaving directory '/home/yzy/tmp/vpp/build/external'
Makefile:734: recipe for target 'external-install' failed
make[1]: *** [external-install] Error 2
make[1]: Leaving directory '/home/yzy/tmp/vpp/build-root'
Makefile:360: recipe for target 'build' failed
make: *** [build] Error 2
```

The reason of this error may be some proxy issue since `www.nasm.us` somehow refuse any http connection recently.

# Solution

What is required here is just a tarball `nasm-2.13.03.tar.xz` which can be easily found [here](https://ftp.osuosl.org/pub/blfs/conglomeration/nasm/). Or we could just download it with `wget`:

```bash
$ cd $path_to_vpp/build/external/downloads
$ wget https://ftp.osuosl.org/pub/blfs/conglomeration/nasm/nasm-2.13.03.tar.xz
```

Now it should be ok. :)
