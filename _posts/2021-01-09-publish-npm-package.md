---
layout: post
title:  "Publishing NPM packages"
date:   2021-01-09 00:00:00 -0600
categories: coding
---
1. Signup for a `npmjs` accout at [https://www.npmjs.com/](https://www.npmjs.com/)
2. Confirm account by going thru the confirmation email (*This is an important step*)
3. Create a `package.json` by executing this command `npm init`
4. Login using `npm login`
5. Publish package by executing this command `npm publish --scope public`

*Note: You'll get a 403 response if the package name is already taken*