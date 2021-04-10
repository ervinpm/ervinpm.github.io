---
layout: post
title:  "Bootstrap in Angular"
date:   2021-01-31 00:00:00 -0600
categories: [coding,angular]
---
To integrate bootsrap into your angular installation, you just need to intall the npm package and include the built files in the configuration

1. Install Bootstrap and JQuery. At the time of writing, jquery is a dependency of bootstrap.
```bash
$ npm install --save bootstrap jquery
```

2. Update `angular.json` to include the built files.
```json
"architect": {
  "build": {
    [...], 
    "styles": [
        "src/styles.css"
      ],
      "scripts": [
        "node_modules/jquery/dist/jquery.min.js",
        "node_modules/bootstrap/dist/js/bootstrap.min.js"
      ]
    },
```

3. import `css` to `styles.scss` so that you can override the styles that fit your app.
```scss
@import "../node_modules/bootstrap/scss/bootstrap";
```