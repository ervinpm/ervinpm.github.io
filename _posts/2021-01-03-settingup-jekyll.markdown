---
layout: post
title:  "Install Jekyll on Windows 10"
date:   2021-01-03 00:00:00 -0600
categories: coding
---

![JekyllIcon](/assets/jekyll-banner.png)

1. Not needed but installing a terminal emulator helps a lot like `cmder`. Installer can be found [here](https://cmder.net/).
2. Also helps to get a good text editor like [vscode](https://code.visualstudio.com/). 
3. Install [Ruby](https://rubyinstaller.org/downloads/). Make sure to install the one that includes DevKit.
4. Install Jekyll by executing this command:
```bash
$ gem install jekyll bundler
```
5. At the time of writing, Ruby 3.0 does not include by default a dependency of Jekyll called `webrick`. In the generetaed jekyll site directory, execute the following command: `bundle add webrick`.