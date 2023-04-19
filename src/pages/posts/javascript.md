---
layout: '../../layouts/MarkdownPost.astro'
title: 'Javascript 学习'
pubDate: 2023-04-08
description: '学习 Javascript 等知识总结'
author: 'Aaron'
cover:
    url: '/preview/jspic.png'
    alt: 'cover'
tags: ["Javascript"]
theme: 'light'
featured: false
---

## async 

## promise
```js
let promise = new Promise(function(resolve, reject) {
  // executor (the producing code, "singer")
});
```
The function passed to new Promise is called the executor. When new Promise is created, the executor runs automatically. It contains the producing code which should eventually produce the result. In terms of the analogy above: the executor is the “singer”.

Its arguments `resolve` and `reject` are callbacks provided by JavaScript itself. Our code is only inside the executor.

When the executor obtains the result, be it soon or late, doesn’t matter, it should call one of these callbacks:

* `resolve(value)` — if the job is finished successfully, with result `value`.
* `reject(error)` — if an error has occurred, `error` is the error object.

The promise object returned by the new Promise constructor has these internal properties:

* `state` — initially "`pending`", then changes to either "`fulfilled`" when `resolve` is called or "`rejected`" when `reject` is called.
* `result` — initially `undefined`, then changes to `value` when `resolve(value)` is called or `error` when `reject(error)` is called.

为避免 callback hell，使用promise。 .then .finally  .catch
如果.then 的链条中有 return promise 的，就等它先完成
every call to a .then returns a new promise, so that we can call the next .then on it.
When a handler returns a value, it becomes the result of that promise, so the next .then is called with it.

## async await
The word “async” before a function means one simple thing: a function always returns a promise.
```js
// works only inside async functions
// The keyword await makes JavaScript wait until that promise settles and returns its result.
let value = await promise;
```
