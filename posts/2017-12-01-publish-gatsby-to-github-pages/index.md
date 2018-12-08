---
title: How to publish your Gatsby source to User or Organization Github pages
date: "2017-12-01"
category: 'gatsby'
tags:
    - gatsby
---

## Why?

As I'm using [User & Organization Github pages](https://help.github.com/articles/user-organization-and-project-pages/), I must use `master` branch as published/generated source and I can not change that. That means I have to use different branch to store Gatsby source code (not generated one), so I chose `source` as a branch name.

## How?

1 - Add `gh-pages` node package as a dev dependency: `yarn add gh-pages --save-dev`

2 - Add this command to `scripts` of `package.json` to do:

- Build Gatsby source to `public` folder
- Use `gh-pages` node package to push `publish` folder to origin `master` branch in Github repo.

```js

"scripts" {
    ...
    "deploy": "gatsby build --prefix-paths && gh-pages -d public -b master",
    ...
}

```

Ta-da, now I can publish my Gatsby site by using single command: `yarn deploy` or `npm run deploy`.

Ok, this is my first post in Gatsby site, I will spend more time to explore more about Gatsby. I'm sure I'll write a lot more interesting things in the future.

Oh, lastly I would like to share my beloved son picture

![My son - MonMon](./mon-mon.jpg)
