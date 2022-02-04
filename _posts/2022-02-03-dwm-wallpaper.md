---
layout: post
title:  "Apply wallpaper for DWM with LightDM"
date:   2022-02-03 00:00:00 -0600
categories: [coding,linux,dwm,lightdm]
---

To apply a wallpaper in dwm and you have lightdm, you can use this solution

## Install feh

```sh
$ sudo pacman -Syu feh
```

## Create a script that will run feh

```sh
$ touch ~/run-dwm
$ chmod 755 ~/run-dwm
```

## Update script

Use your preferred editor to edit the script

```sh
$ vim ~/run-dwm
```

Add the following lines. Change the path to your desired wallpaper

```bash
#!/bin/bash

feh --bg-fill /home/user/Pictures/wallpaper/003.jpeg
dwm
```

## Update `.desktop` file

```sh
$ sudo vim /usr/share/xsessions/dwm.desktop
```

Change the value to the path of the created script

```sh
[Desktop Entry]
Encoding=UTF-8
Name=Dwm
Comment=dynamic window manager
Exec=/home/user/run-dwm
Icon=dwm
Type=XSession
```

Restart your dwm session

## Reference

https://bbs.archlinux.org/viewtopic.php?id=191898
