---
layout: post
title:  "Install MongoDB on Windows 10"
date:   2021-01-10 00:00:00 -0600
categories: coding
---
When developing apps on your local machine, you may want the portable mongodb server. You'll need to do a few steps to run it:

1. Download the windows zip file from the Mongo DB [site](https://www.mongodb.com/try/download/community)
2. Extract to an simple folder (i.e. C:\mongodb)
3. Create a folder inside the binary folder named `data` and under that folder create another one called `db`. It should have a path like this: `C:\mongodb\bin\data\db`
4. Change directory to the `bin` folder and run `./mongodb.exe  --dbpath ./data/db`