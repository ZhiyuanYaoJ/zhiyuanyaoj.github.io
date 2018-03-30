---
title: My Linux Configuration
date: 2018-03-26
description: Note down all my configuration of Ubuntu 16.04 in VirtualBox for reference in the future. 
categories:
- Linux
tags:
- Linux
photos:
-
---

# Intro

In order to play with [fd.io - VPP](https://wiki.fd.io/view/VPP/What_is_VPP%3F), which is the open source version of Cisco's Vector Packet Processing (VPP) technology, I decided to have a long-term Ubuntu system on my MacBook Pro.

# Virtual Machine

1. Download and install [virtual-box](https://www.virtualbox.org)
2. Download a recent [Ubuntu Desktop ISO (Version 16.04)](https://www.ubuntu.com/download/desktop)
3. Create a new VM with:
    - 80GB disk space (Fixed-size VDI makes it faster)
    - ≥4GB of memory
    - 2 cores (_VM Settings_ -> _System_ -> _Processor_)
    - Enable shared clipboard (install **Guest Addition**, then set from _VM Settings_ -> _General_ -> _Advanced_, see Reference [[1]](https://superuser.com/questions/42134/how-do-i-enable-the-shared-clipboard-in-virtualbox) & [[2]](https://askubuntu.com/questions/73059/how-to-copy-paste-from-ubuntu-virtualbox-guest-to-windows-host))
    - Make sure acceleration is enabled (_VM Settings_ -> _System_ -> _acceleration_)
    - Enhance display capability (_VM Settings_ -> _Display_ -> _Screen_: assign enough **Video Memory** and **Enable 3D Acceleration**, see Reference [[3]](http://www.xuzefeng.com/post/85.html))
4. Install Ubuntu on the VM by selecting the Ubuntu ISO when start the VM (Choose _Erase disk and install Ubuntu_)

# System

It is really time-consuming if we want a highly customized Ubuntu system. But it turns out that the whole procedure can be interesting and rewarding (to some extent :) ).

The results are shown below.

![Desktop](https://zhiyuanyaoj.github.io/assets/images/ubuntu/vm-ubuntu-dt.png)

![Zsh + Vim](https://zhiyuanyaoj.github.io/assets/images/ubuntu/zsh-vim.png)

![Tmux](https://zhiyuanyaoj.github.io/assets/images/ubuntu/tmux.png)

## Cleanup & Update

```bash
# My own preference is to use Chrome instead of Firefox :)
$ dpkg --get-selections | grep firefox     # List all components of Firefox
$ sudo apt purge firefox firefox-locale-en unity-scope-firefoxbook

# Erase libreoffice since I don't need it in my VM
$ sudo apt purge libreoffice-common

# Remove Amazon
$ sudo apt purge unity-webapps-common

# Cleanup un-useful apps
$ sudo apt purge thunderbird totem rhythmbox empathy brasero simple-scan gnome-mahjongg aisleriot
$ sudo apt purge gnome-mines cheese transmission-common gnome-orca webbrowser-app gnome-sudoku
$ sudo apt purge onboard deja-dup

# Clear older version software cache
$ sudo apt autoclean
# Clear all software cache
$sudo apt clean
# Remove isolated softwares which will not be used again
$ sudo apt autoremove

# Update
$ sudo apt update
$ sudo apt upgrade
```

## Make it beautiful

```bash
# Unity Management Tool (GUI)
$ sudo apt install unity-tweak-tool
# Flatabulous Theme
$ sudo add-apt-repository ppa:noobslab/themes
$ sudo apt update
$ sudo apt install flatabulous-theme
# Flatabulous Theme icon
$ sudo add-apt-repository ppa:noobslab/icons
$ sudo apt update
$ sudo apt install ultra-flat-icons
# Ubuntu version Alfred
$ sudo add-apt-repository ppa:noobslab/macbuntu
$ sudo apt update
$ sudo apt install albert
```

# Software

## Git

```bash
$ sudo apt install git
```

## Wget

```bash
$ sudo apt install wget
```

## Terminator (bash)

```bash
$ sudo add-apt-repository ppa:gnome-terminator
$ sudo apt update
$ sudo apt install terminator
```

> Terminator should be setup as default now. Restart the terminal with shortcut: "Ctrl+Alt+T"

## ZSH

```bash
$ sudo apt install zsh
$ chsh -s /bin/zsh
```

> Exit and re-enter the bash

```bash
# Check current bash
$ echo $SHELL
```

## Oh-My-ZSH

### Installation

1. Auto Installation
```bash
$ wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

2. Manual Installation
```bash
$ git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
$ cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

### Theme Setup

#### Install powerline font

```bash
$ cd
$ wget https://github.com/powerline/powerline/raw/develop/font/PowerlineSymbols.otf
$ wget https://github.com/powerline/powerline/raw/develop/font/10-powerline-symbols.conf
$ mv PowerlineSymbols.otf ~/.fonts/
$ mkdir -p .config/fontconfig/conf.d #if directory doesn't exists
```

#### Clean fonts cache

```bash
$ fc-cache -vf ~/.fonts
```

#### Move config file

```bash
$ mv 10-powerline-symbols.conf ~/.config/fontconfig/conf.d
```

#### Change theme colors to solarize

`dconf` is required if you don't already have it.

```bash
$ sudo apt-get install dconf-cli
```

```bash
$ git clone git://github.com/sigurdga/gnome-terminal-colors-solarized.git ~/.solarized
$ cd ~/.solarized
$ ./install.sh
```

 - I recommend you **option 1** (dark theme) which is just great.  
 - Choose **option 1** to download seebi' dircolors-solarized
 
After installation, open `.zshrc` and add the line:

```bash
eval `dircolors ~/.dir_colors/dircolors`
```

To activate dark solarize theme in Terminator just right click on the terminal, 
> Preferences>Profiles>Colors>Foreground and Background>Built-in schemes: Solarized dark
> Preferences>Profiles>Colors>Palette>Built-in schemes: Solarized

Restart Terminator and you're done!

#### Configuration

My configuration files looks like this:

```bash
...
ZSH_THEME="agnoster"
...
plugins=(
    git zsh-autosuggestions autojump ubuntu
)
source $ZSH/oh-my-zsh.sh
...
# Context: user@hostname (who am I and where am I)
prompt_context() {
  if [[ "$USER" != "$DEFAULT_USER" || -n "$SSH_CLIENT" ]]; then
    prompt_segment black default "%(!.%{%F{yellow}%}.)$USER"
  fi
}
...
alias vim='sudo vim'
alias zshconfig="vim ~/.zshrc"
alias ohmyzsh="vim ~/.oh-my-zsh"
alias javac="javac -J-Dfile.encoding=utf8"
alias grep="grep --color=auto" 
alias -s html=vi
alias -s rb=vi
alias -s js=vi
alias -s c=vi
alias -s java=vi
alias -s txt=vi
alias cls='clear' 
alias ll='ls -al'
alias la='ls -a'
alias top10='print -l  ${(o)history%% *} | uniq -c | sort -nr | head -n 10'
...
# Autojump
[[ -s ~/.autojump/etc/profile.d/autojump.zsh ]] && . ~/.autojump/etc/profile.d/autojump.zsh
autoload -U compinit && compinit -u
...
eval `dircolors ~/.dir_colors/dircolors`
```

> Remember to install the plugin _autojump_ with:

```bash
$ git clone https://github.com/joelthelion/autojump.git
$ cd autojump
$ ./install.py
$ cd ../
$ rm -rf autojump
```

> and then add the following lines into `~/.zshrc`

```
[[ -s ~/.autojump/etc/profile.d/autojump.zsh ]] && . ~/.autojump/etc/profile.d/autojump.zsh
autoload -U compinit && compinit -u
```

> Reload `ZSH` and it's good to go with `$ j + $PATH`

## Vim

```bash
$ sudo apt install vim
```

Sophisticated setup can be found in Reference [[8]](https://github.com/yangyangwithgnu/use_vim_as_ide). I won't dive deep into the details. It seems pretty nice in the end, but... the whole procedure was just way too complicated. Finally, I decided to go with [VSCode](https://code.visualstudio.com/download) as my IDE.

Good luck configuring! :)

## TMux

```bash
$ sudo apt install tmux
```

## F.Lux

```bash
$ sudo add-apt-repository ppa:nathan-renniewaldock/flux
$ sudo apt-get update
$ sudo apt-get install fluxgui
```

## System Load Indicator

```bash
$ sudo add-apt-repository ppa:indicator-multiload/stable-daily
$ sudo apt update
$ sudo apt install indicator-multiload
```

## Chrome

1. Add Key:
```bash
$ wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
```

2. Set repository:
```bash
$ echo 'deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main' | sudo tee /etc/apt/sources.list.d/google-chrome.list
```

3. Install package:
```bash 
$ sudo apt-get update 
$ sudo apt-get install google-chrome-stable
```

## Visual Studio Code

Download from: https://code.visualstudio.com/download

- Extensions:
    + C/C++
    + C/C++ Clang Command Adapter
    + C++ Intellisense
    + Git Blame
    + Git History
    + markdownlint

## VPP

Since I need to have fun with VPP, I just put the instructions to install VPP here.

```bash
$ cd $PATH_YOU_WANT_TO_HAVE_VPP
$ git clone https://git.fd.io/vpp
$ cd vpp
# It'll take a little while to install all the dependencies
$ make install-dep
# It'll take a little more while to build the whole program
$ make build
# It should take you to the interactive terminal of VPP :)
$ make run
```

# Reference

- \[1\] - [How do I enable the shared clipboard in VirtualBox?](https://superuser.com/questions/42134/how-do-i-enable-the-shared-clipboard-in-virtualbox)
- \[2\] - [How to copy & paste from Ubuntu VirtualBox guest to Windows host?](https://askubuntu.com/questions/73059/how-to-copy-paste-from-ubuntu-virtualbox-guest-to-windows-host)
- \[3\] - [VirtualBox虚拟机运行Ubuntu如何不卡](http://www.xuzefeng.com/post/85.html)
- \[4\] - [Ubuntu 16.04 + Terminator + Oh My ZSH with Agnoster Theme](https://gist.github.com/renshuki/3cf3de6e7f00fa7e744a)
- \[5\] - [Ubuntu 调优与美化教程](https://zhuanlan.zhihu.com/p/26032793)
- \[6\] - [Ubuntu 安装zsh配置 oh my zsh autojump apt-get 使用](https://www.jianshu.com/p/fc63d64c06d5)
- \[7\] - [强大的zsh配置文件](http://www.cnblogs.com/ma6174/archive/2012/05/08/2490921.html)
- \[8\] - [所需即所获：像 IDE 一样使用 vim](https://github.com/yangyangwithgnu/use_vim_as_ide)
- \[9\] - [打造Python开发工具——vim+zsh+tmux](https://zhuanlan.zhihu.com/p/26000126)
- \[10\] - [What is VPP?](https://wiki.fd.io/view/VPP/What_is_VPP%3F)
- \[11\] - [你的Ubuntu还可以这么美](https://zhuanlan.zhihu.com/p/27467392)