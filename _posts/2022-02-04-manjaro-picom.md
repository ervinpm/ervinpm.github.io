---
layout: post
title:  "Build and Install picom in Manjaro"
date:   2022-02-03 00:00:00 -0600
categories: [coding,linux,dwm,lightdm]
---


## Dependencies

### meson

```sh
pip3 install meson
```

## libev
```sh
sudo pacman -Syu libev
```

## libconfig
```sh
sudo pacman -Syu libconfig
```

## install
```sh
meson configure -Dprefix=~/.local build
ninja -C build install
```

## copy sample conf
```sh
mkdir ~/.config/picom
cp picom.sample.conf ~/.config/picom/picom.conf
```

## picom
```sh
picom -b
```

## Show configured VGA
```sh
mhwd -li
```

## Fix inactive window blur
https://giters.com/jonaburg/picom/issues/50
