---
layout: post
title:  "Updating cron tab editor"
date:   2021-03-22 00:00:00 -0600
categories: [coding,linux,crontab]
---

If you want to update the default editor of cron tab, you'll have to update your `.bashrc` file.


## Updating .bashrc

Add the following line to your `.bashrc`

```bashrc
export VISUAL="vim"
```

## Reload the configuration

To reload without rebooting your machine execute the following command:

```shell
$ source ~/.bashrc
```