---
layout: post
title:  "Bad Interpreter in WSL2"
date:   2021-03-07 00:00:00 -0600
categories: [coding,bash,win10,ubuntu]
---
# Bad Interpreter in WSL2

While using WSL2 to manage my jekyll posts, i ran into a problem where in the jekyll commands were getting a `bad interpreter: No such file or directory` error. After some research, the problem was because it was conflicting with a jekyll path installed on my win10 machine. Fixing this was fairly easy to do:

1. Create or update the `wsl.conf`
```bash
$ sudo vim /etc/wsl.conf
```

2. Add the following lines
```conf
[interop]
appendWindowsPath = false
```

3. Save and exit `:x`
4. Restart Ubuntu or your terminal