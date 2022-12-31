---
layout: post
title:  "Create a git server"
date:   2022-12-31 00:00:00 -0600
categories: [coding,linux,git]
---

To create a git server

## Create a git user

Note: step might be optional when user `git` already exists

```sh
$ sudo useradd -m git
```

## Create the home folder for git

```sh
$ sudo mkdir /home/git
$ sudo chmod 700 /home/git
$ sudo chown -R git:git /home
```

## Create `.ssh` folder

```sh
$ sudo su git -s /bin/bash
$ mkdir .ssh
$ chmod 700 .ssh
```

## Create `authorized_keys`

```sh
$ touch .ssh/authorized_keys
$ chmod 600 .ssh/authorized_keys
```

Copy your public keys to this file

## Create a repository

```sh
$ mkdir hello-world.git
```

## Cloning the repo

```sh
$ git clone git@myserver.home:hello-world.git
```
