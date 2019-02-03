---
title: Python - OpenCV Installation
date: 2019-02-03
description: Avoid some import errors
categories:
- Python
tags:
- Python
---

# Error

```bash
ImportError: libcblas.so.3: cannot open shared object file: No such file or directory
ImportError: libjasper.so.1: cannot open shared object file: No such file or directory
```

# Solution

```bash
pip3 install opencv-python
sudo apt-get install libatlas-base-dev
sudo apt-get install libjasper-dev
sudo apt-get install libqtgui4
sudo apt-get install python3-pyqt5
sudo apt-get install libqt4-test
```

# Other

Pillow might also be interesting for image processing.

```bash
# installation
pip3 install Pillow
```

# Reference

- [GitHub pyparrot - Issue #34](https://github.com/amymcgovern/pyparrot/issues/34)