---
title: An error when building Gatsby
date: "2017-12-26"
category: 'gatsby'
tags:
    - gatsby
---



If you see below error when building Gatsby


```bash
error Plugin gatsby-transformer-remark returned an error

Error: dlopen(/Users/tthanhdang/src/_my-src/tung-dang/node_modules/sharp/build/Release/sharp.node, 1): Library not loaded: @rpath/libvips-cpp.42.dylib
    Referenced from: /Users/tthanhdang/src/_my-src/tung-dang/node_modules/sharp/build/Release/sharp.node
    Reason: image not found
  
  - module.js:11 require
    internal/module.js:11:18
  
  - constructor.js:8 Object.<anonymous>
    [tung-dang]/[sharp]/lib/constructor.js:8:15
  
  - module.js:11 require
    internal/module.js:11:18
  
  - index.js:3 Object.<anonymous>
    [tung-dang]/[sharp]/lib/index.js:3:15
    
```



I'm not sure what the root cause is. I got the above error twice a day after adding & removing packages several times on that day. I guess after adding & removing packages, somehow my `node_modules` is corrupt. 

My solution to fix above error is to remove `node_modules` folder and then try to `yarn install` again.



In order to clean `node_modules` and other stuff quicker, I setup a script to clean up for me.

```js
// in package.json
"scripts": {
  ...  
  "clean": "./scripts/clean.sh",
  ...
}
```

Here is the bash script looks like

```bash
#!/bin/bash
rm -rf node_modules
rm -rf .cache
rm -rf public
```

So I just need to run this command to help me clean up everything I need.

```js
yarn run clean // not `yarn clean`
```



Hope this post can save you several hours to fix the error. If you find a root cause, please share it with me. Happy coding!
