---
layout: post
title:  "Fixing UID or GID errors in podman"
date:   2023-11-14 00:00:00 -0600
categories: [coding,linux,podman]
---

When you encounter an error message like this `potentially insufficient UIDs or
GIDs available in user namespace` when running podman. Update the configuration
in `/etc/containers/storage.conf` and set the value to `true`.

```
ignore_chown_errors = "true"
```

