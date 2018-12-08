---
title: Collection in JS examples
date: "2018-01-05"
category: 'js'
isPrivate: true
tags:
    - js
---

## 1

"sleep" in JavaScript 😴: Delay execution of an asynchronous function by putting it to sleep, returning a Promise.

Source: Addy Osmani

```js

const sleep = ms = new Promise(resolve => setTimeout(resolve ms));

async function sleepWork() {
    console.log('I\'m going to sleep for 1 second');
    await sleep(1000);
    console.log('I woke up after 1 second);
}

sleepWork();
// Output
// > I'm going to sleep for 1 second
// ..waits for 1 second..
// > I woke up after 1 second.

```

## 2

Tip: Get the unique values of an array. Use ES2015 Set() and the ...rest operator to discard duplicate values.

```js

const uniqueArray = arr => [...new Set(arr)];
uniqueArray([1, 2, 2, 3]);
// 1,2,3

```
