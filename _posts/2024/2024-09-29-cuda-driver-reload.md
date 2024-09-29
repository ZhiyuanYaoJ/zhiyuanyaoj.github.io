---
title: Resolving Nvidia Driver Reload Issue - Kernel Module Version Mismatch
date: 2024-09-29
description: When working with Nvidia drivers on Linux, you may encounter a version mismatch between the Nvidia kernel module and the installed library. This can prevent the GPU from functioning properly without a system reboot. In this guide, I walk through the process of manually unloading and reloading the Nvidia kernel modules to resolve the issue without restarting the machine.
categories:
- Linux
tags:
- Nvidia
- Linux
- Kernel
- Driver Issue
- CUDA
- GPU
- Troubleshooting
photos:
---

## Overview

Recently, I encountered an issue when running the `nvidia-smi` command, where it failed due to a driver/library version mismatch.

```bash
~ nvidia-smi
Failed to initialize NVML: Driver/library version mismatch
NVML library version: 535.183
```

This issue occurs when the Nvidia kernel module version is not updated correctly. Typically, restarting the machine resolves the problem, as it reloads the updated kernel module. However, if a reboot is not an option, we can manually reload the Nvidia kernel modules. Here's the step-by-step process:

## Step 1: Unload the Nvidia Kernel Module

To unload the Nvidia kernel module, first, we need to stop any processes using the Nvidia driver:

```bash
sudo rmmod nvidia
```

If this command returns an error indicating that the Nvidia module is in use, we can identify the dependent modules using the following command:

```bash
lsmod | grep nvidia
```

You'll likely see output like this:

```bash
nvidia_uvm           1540096  0
nvidia_drm             77824  0
nvidia_modeset       1306624  1 nvidia_drm
nvidia              56692736  3 nvidia_uvm,gdrdrv,nvidia_modeset # <- This is the line we need to take a look at
```

These modules must be unloaded before we can remove the Nvidia kernel module.

```bash
# remove all the modules that are listed on the line above and their potential dependencies
sudo rmmod nvidia_uvm
sudo rmmod gdrdrv
sudo rmmod nvidia_modeset
sudo rmmod nvidia_drm
sudo rmmod nvidia
```

If successful, the Nvidia modules will be completely unloaded from the system.

## Step 2: Reload the Nvidia Kernel Module

Once the Nvidia modules are unloaded, simply running the `nvidia-smi` command will trigger the automatic loading of the correct Nvidia kernel module:

```bash
sudo nvidia-smi
```

You'll then see an output similar to the one below, indicating that the Nvidia drivers are working correctly:

```bash
Sun Sep 29 13:05:55 2024
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.183.01             Driver Version: 535.183.01   CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  Tesla T4                       Off | 00000000:00:1B.0 Off |                    0 |
| N/A   27C    P0              25W /  70W |      0MiB / 15360MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
|   1  Tesla T4                       Off | 00000000:00:1C.0 Off |                    0 |
| N/A   27C    P0              24W /  70W |      0MiB / 15360MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
|   2  Tesla T4                       Off | 00000000:00:1D.0 Off |                    0 |
| N/A   28C    P0              24W /  70W |      0MiB / 15360MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
|   3  Tesla T4                       Off | 00000000:00:1E.0 Off |                    0 |
| N/A   27C    P0              18W /  70W |      0MiB / 15360MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+

+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|  No running processes found                                                           |
+---------------------------------------------------------------------------------------+
```

## Reflection

### Why Does This Issue Happen?

This issue arises because the Nvidia driver version loaded into the kernel doesn't match the library version. While rebooting the system can resolve the mismatch, the above steps offer a manual solution when rebooting isn't feasible.

### Conclusion

The process involves two main steps:

1. **Unload the Nvidia kernel module**:
   - Use `rmmod` to unload any dependent Nvidia kernel modules.
2. **Reload the Nvidia kernel module**:
   - Run `nvidia-smi` to automatically reload the kernel module.

This simple workaround allows you to resolve Nvidia driver issues without restarting your machine.


### Reference

- [解决Driver/library version mismatch](https://comzyh.com/blog/archives/967/)