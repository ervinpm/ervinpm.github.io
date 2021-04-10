---
layout: post
title:  "WSL2 Copy Paste"
date:   2021-04-10 00:00:00 -0600
categories: [coding,linux,windows,wsl2,vim]
---

When yanking text in WSL vim, it does not automatically transfer over to the windows clipboard. To do this there is a workaround to use vim-gtk and x server for windows

## Install VIM GTK

VIM GTK is needed because the default vim installation does not have clipboard installed by default.
```sh
$ sudo apt install vim-gtk
```

## X server for Windows

1. Download and install from <https://sourceforge.net/projects/vcxsrv/>
2. During the installation, enable the following:
- Multiple windows
- Start no client
- Extra Settings
  - Clipboard
    - Primary selection
  - Native opengl
  - Disable access control
3. Save your configuration
4. Copy the configuration to: `C:\Users\<yourawesomename>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup`

## WSL2 setup

In your wsl terminal, execute these commands to integrate the x server
```sh
$ LOCAL_IP=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}')
$ export DISPLAY=$LOCAL_IP:0
```

