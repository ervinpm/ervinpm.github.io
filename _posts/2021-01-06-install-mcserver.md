---
layout: post
title:  "Install Minecraft Server on Arch Linux"
date:   2021-01-06 00:00:00 -0600
categories: [coding,linux,minecraft]
---
1. Install `Java` for linux
```bash
$ sudo pacman -Syu java
```

2. If you want to run mcserver in the background, you can install screen
```bash
$ sudo pacman -Syu screen
```

3. Create a folder where you want to store the minecraft server
```bash
$ mkdir mcserver
$ cd mcserver
```

4. Download minecraft server from [mojang](https://www.minecraft.net/en-us/download/server)
```bash
$ wget https://launcher.mojang.com/v1/objects/35139deedbd5182953cf1caa23835da59ca3d7cd/server.jar
```

5. Create a new screen session
```bash
$ screen -S mcserver
```

6. Run the server
```bash
$ java -Xmx1024M -Xms1024M -jar minecraft_server.1.16.4.jar nogui 
```

7. Minimize or set the server to background by pressing `CTRL + a, d`

> Note: During the first run, the server will generate `eula.txt`. Update the text file and set the value from `eula=false` to `eula=true`