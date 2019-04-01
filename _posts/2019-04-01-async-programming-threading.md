---
layout: post
title: Async programming and threading
tags:
- DotNet
- JavaScript
---

Read this article to know about multi task application: [Single task and multi task app](2019-03-31-single-multi-task-app.md)

We know that all applications have the main thread, which handles user inputs, UI rendering. And we know that main thread should not be too busy handling any heavy stuffs. All heavy stuffs need to be offloaded into either:

- Background (worker) threads, created inside the application
- System (OS, runtime environment), via system calls

Some languages, like C#, support both methods. You can create many worker threads, or request the system to do heavy works.

Some languages, like Javascript, only support the later method. Because JS is single-thread by nature, it can't spawn background threads. So to process heavy works, it must use system calls, via asynchronous programming model.

Let's walk through some examples.

# Async programming with Javascript

JS is single thread by nature.

At the beginning, we write code with function callback:

```js
var callback = function (result) {
    console.log('Heavy stuff done', result);
}

doHeavyStuffAsync(callback);
console.log('Phew, heavy stuff offloaded to system');
```

It's simple: `doHeavyStuffAsync` tells system to do something, and tell system to call `callback` when it finished.

All those code are executed in main thread. `doHeavyStuffAsync` is actually very light weight, it's not the actual heavy work, it's just a request to system for heavy work. So it's ok to run in main thread.

Then `promise` is invented - just a difference way to issue async operations:

```js
doSomethingFirst()
    .then(function(res) {
        doSomethingNext(function(res) {
            console.log('Done')
        })
    })
```

Still look similar to callback method, right?

And then, `async` and `await`

```js
await doSomethingFirst()
await doSomethingNext()
console.log('Done')
```

Nothing similar to callback and promise method. Let take a closer look.

## Javascript async - await

Example using callback:

```js
function sampleAsyncFunction() {
    doHeavyStuffAsync(function (result) {
        console.log('Heavy stuff done', result);
    });
}

sampleAsyncFunction();
console.log('Phew, heavy stuff offloaded to system');
```

Example using async await:

```js
async function sampleAsyncFunction() {
    const result = await doHeavyStuffAsync();
    console.log('Heavy stuff done', result);
}

sampleAsyncFunction();
console.log('Phew, heavy stuff offloaded to system');
```

The code run as normal until it reachs `await` statement inside function `sampleAsyncFunction()`. Here something happens underlying:

- Code splitting: imagine all lines after `await` (but still inside that function: the assignment to `result` and the `console.log`) will be splitted into a separated function. This function will be executed by system, after `await` done. This function is similar to the callback function in the first example.
- Yield control to the caller of `sampleAsyncFunction`: So because the code lines after `await` are splitted to another function, so the function `sampleAsyncFunction` has nothing more to do and will return immediately, and continue in its caller flow.

In short, `await` will split the remain code into callback function, and return.

# Async programming in C#

There are some great articles:

- [https://docs.microsoft.com/en-us/dotnet/csharp/async](https://docs.microsoft.com/en-us/dotnet/csharp/async)
- [https://docs.microsoft.com/en-us/dotnet/standard/async-in-depth](https://docs.microsoft.com/en-us/dotnet/standard/async-in-depth)

And great post: [https://blogs.msdn.microsoft.com/pfxteam/2012/04/12/asyncawait-faq/](https://blogs.msdn.microsoft.com/pfxteam/2012/04/12/asyncawait-faq/)

# Async or sync

[There are 2 type of async operations](2019-03-31-single-multi-task-app.md):

- I/O-bound operations: which relate to networking (e.g. requests to server), or file system accessing(e.g. read/write to file system)
- CPU-bound operations: which relate to computation (e.g. arithmetic calculation on large dataset, image processing)

In Javascript, only IO operation is truly async. CPU operation will act like synchronous operation.  
That's why when you `await` for IO operation, your main thread is free to do anything else.  
But when you `await` on heavy CPU operation, the main thread is blocked and UI is freezing. Because, they are executed in main thread.

In C#, CPU operation can be offloaded into worker thread. But the question is: [should I expose asynchronous wrappers for synchronous methods?](https://devblogs.microsoft.com/pfxteam/should-i-expose-asynchronous-wrappers-for-synchronous-methods/)

In short:

- For scalability: NO
- For offloading: mostly NO, but with some exception
