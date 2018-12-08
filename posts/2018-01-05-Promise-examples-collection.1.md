---
title: Promise exmples collection.
date: "2018-01-05"
category: 'js'
isPrivate: true
tags:
    - js
    - promise
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

Source: Addy Osmani
Tip: Promisify a JavaScript function so it returns a promise. Use currying to return a function, returning a promise calling the original function.

In Node 8, you can use util.promisify() to achieve the same.

```js

const promisify = func => (...args) => {
    return new Promise((resolve, reject) => {
        const callback = (error, result)) => {
            error ? reject(error) : resolve(result);
        };
        func(...args, callback);
    });
};

// Example: a simple time-out function
const delayFn = (delay, cb) => setTimeout(cb, delay);
// Promisified version of the function
const promisifiedDelay = promisify(delayFn);
// Promise resolves after 2 seconds
promisifiedDelay(2000).then(() => console.log('Hey, there!'));

```

## 3

Tip: Run an array of JavaScript Promises in series. Uses Array.reduce() to create a Promise chain. Each Promise returns the next Promise when resolved.


```js

const runPromisesInSeries = ps
    => ps.reduce((p, next)
    => p.then(next), Promise.resolve());

const delay = delay => new Promise(r => setTimeout(r, delay));
runPromisesInSeries([
    () => delay(1000),
    () => delay(2000),
]);
// executes each promise sequentiall, taking a total of 3 seconds to complete.
```
