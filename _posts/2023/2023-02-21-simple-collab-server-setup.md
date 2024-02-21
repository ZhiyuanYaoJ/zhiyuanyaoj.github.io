---
title: Simple Collab Server Setup
date: 2023-02-21
description: To collaborate with folks on a shared server, some simplistic setups are noted down here. This case applies for people who prefer zsh to bash, and you are the only one using zsh on the machine.
categories:
- Linux
tags:
- Setup
- Ubuntu
photos:
- 
---

## Zsh Installations

Install zsh: 

```bash
sudo apt install zsh
```

Install oh-my-zsh: 

```bash
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

Note that you probably don't want to set zsh as the default shell.

## TMux Configuration

Set the zsh as the default shell in tmux. Add the following line to `~/.tmux.conf`:

```
set-option -g default-shell /bin/zsh
```

## Miniconda Installation

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
```

## Plug-ins

### Zsh-Syntax-Highlighting

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

Then add `zsh-syntax-highlighting` to the `~/.zshrc`:

```bash
plugins=(git zsh-syntax-highlighting)
```

### Auto-Suggestion

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

Then add `zsh-autosuggestions` to the `~/.zshrc`:

```bash
plugins=(git zsh-syntax-highlighting zsh-autosuggestions)
```