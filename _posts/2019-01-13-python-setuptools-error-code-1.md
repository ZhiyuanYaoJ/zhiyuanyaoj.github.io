---
title: setuptools pkg_resources pip wheel failed with error code 1
date: 2019-01-13
description: A brief solution to setuptools error when creating virtualenv.
categories:
- Python
tags:
- Python
photos:
-
---

# Solution

Well, it seems to be a local variable issue, which can be easily resolved by entering two lines below in terminal:

```bash
export LC_ALL="en_US.UTF-8"
export LC_CTYPE="en_US.UTF-8"
```

# Reference

- [GitHub Certbot - Issue #2883](https://github.com/certbot/certbot/issues/2883)