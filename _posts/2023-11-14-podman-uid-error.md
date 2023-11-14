---
layout: post
title:  "Fixing UID or GID errors in podman"
date:   2023-11-14 00:00:00 -0600
categories: [coding,linux,podman]
---

## Solution 1

This is an error that I have encountered when running podman for the first
time. It looks like it is caused by running it in a rootless mode. More
information can be found here
[here](https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md).

Here are the steps that I did to resolve it replace `user name` with your username:

```sh
$ usermod --add-subuids 100000-165535 --add-subgids 100000-165535 [user name]
$ grep [user name] /etc/subuid /etc/subgid
```

## Solution 2 (Not recommended)

When you encounter an error message like this `potentially insufficient UIDs or
GIDs available in user namespace` when running podman, update the configuration
in `/etc/containers/storage.conf` and set the value to `true`.

```
ignore_chown_errors = "true"
```

