---
title: Ubuntu 16.04 - Unity Issues
date: 2019-02-25
description: Solution to some keyboard shortcut binded unity GUI problems w/ ubuntu 16.04.
categories:
- Linux
tags:
- Linux
photos:
-
---

# Problem 1 - Messy alt + tab

## Description

When I press `Alt`+`Tab`, two interfaces show up, with one from unity, and the other from some next window previewer.

## Solution

Install CompizConfig Settings Manager:

```bash
sudo apt-get install compizconfig-settings-manager
```

Then open it by searching `compizconfig`. Click `Window Manager` -> `Application Switcher`, then disable `Next Window (keyboard)`. 

# Problem 2 - superkey + direction key 

## Description

Coming from CentOS / Ubuntu 18.04, I've got used with `superkey` + `left/right` to split screen. Back in ubuntu 16.04, this key binding doesn't seem to work.

## Solution

With `CompizConfig Settings Manager` installed as is instructed above, we can check `Grid` option is checked. Then we can use `ctrl` + `superkey` + `left / right` to split screen.

# Ref
- \[1\] [Ask Ubuntu - ubuntu 16.04 alt + tab not working properly](http://users.ece.cmu.edu/~vsekar/papers/sigcomm15_mpcdash.pdf)<a name="ref1"></a>
- \[2\] [Ask Ubuntu - Ctrl + Super + Left/Right not working in Ubuntu 16.04](https://askubuntu.com/questions/821954/ctrl-super-left-right-not-working-in-ubuntu-16-04)<a name="ref2"></a>