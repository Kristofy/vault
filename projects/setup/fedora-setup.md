---
title:  Fedora Setup
created: 2024-07-09
draft: true
publish: false
tags: 

---
# Fedora Setup

This is a guide to set up my fedora system for day to day use.

Updated system with
```bash
$ dnf update
```

added yadm repo to dnf with 
```bash
$ curl -fLo /usr/local/bin/yadm https://github.com/TheLocehiliosan/yadm/raw/master/yadm && chmod a+x /usr/local/bin/yadm
```

get my config with
```bash
$ yadm get https://github.com/Kristofy/conf.git
```

decrypt the secrets with 
```bash
$ yadm decrypt
```

change the yadm remote to ssh with 
```bash
$ yadm remote remove origin 
$ yadm remote add origin git@github.com:Kristofy/conf.git
```

## Set up initial
Install a few packages
```bash
$ sudo dnf install -y alacritty zsh tmux neovim htop fzf ripgrep podman distrobox xpanes eza
$ pip3 install podman-compose
```

Install nerd font
- get jetbrains mono font from https://www.jetbrains.com/lp/mono/#how-to-install
- install the nerd font https://www.nerdfonts.com/font-downloads

```bash
$ curl -L -s https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip -o - | unzip -d /usr/share/fonts/jet-brains-mono/ -
$ fc-cache -f -v
```

Inside kdesettings set alacritty as the default terminal


Set zsh as default shell with `chsh -s $(which zsh)` or `sudo lchsh $USER` on fedora
```bash
$ sudo lchsh $USER
```

Add the zsh dotfile to the env echo ZDOTDIR="/home/kristof/.config/zsh" >> /etc/zsh/zshenv
```bash
$ echo ZDOTDIR="/home/kristof/.config/zsh" >> /etc/zsh/zshenv
```

Install tpm for tmux
git clone https://github.com/tmux-plugins/tpm ~/.config/tmux/plugins/tpm
	press &lt; Tmux prefix &gt; + I (capital I) to install the plugins inside tmux

# Customize settings

shortcuts
krunner
theme
desktop - menu

TODOs
- neogit

Also install
- kdotool (Everyting is out of date, use the source and cargo build --release)
- ydotool
- put dev/utils/dev into the path
- put dev/utils/tab into the path
- Add the tab script to vsudo like so
<!-- kristof ALL=(ALL) NOPASSWD: /usr/bin/scrcpy -->
<!-- kristof ALL=(ALL) NOPASSWD: /usr/bin/kdotool -->

Nice script to list the sizes of each commit as if it was not a git repository
# TODO restore to the commit you started from 

```bash
$ git log --pretty=format:"%h" |  xargs -Ixx zsh -c 'git checkout -q xx && du -hs --exclude=".git" | cut -f1 | xargs -Iyy echo xx: yy'
```

