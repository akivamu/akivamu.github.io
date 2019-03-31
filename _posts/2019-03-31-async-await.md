---
layout: post
title: async await and thread
tags:
- DotNet
- JavaScript
---

# Single task application

When you open a console (or cmd line, in MS-DOS or Linux whatever), and you type a command and
press enter, something will run sequentially (e.g. finding some file/folder).  
The control is blocked, we can't type anything to the console until the command finished.

We can call that program is single-thread (or single task) application: one task at a time.  
(This is not absolutely true, but for simplicity)

We can see that the user interaction is blocked when program is running, it's not so convenience
and nowaday we call it: bad user experience.  
So the engineer invents new design: multi-task application

# Multi task application

In multi task application, the user interface is never blocked (at least it's the goal). User can interact/perform other operations while something happens in background.  
For example: youtube can play video while we are typing comments. Facebook app can prefetch new
content while we are scrolling...

To achieve that functionality, there are some methods, let's go through each.

## Main thread and background threads

In this method, the application (or system) uses multiple threads:

- Single main thread (aka UI thread): handles user interaction, rendering UI
- Multiple background threads (aka worker threads): process heavy works in the background (like
loading data from internet, heavy computation works...)

Basically, the main thread is an infinite loop which monitoring a queue of events. Event could be an user interaction (e.g. typing, touching...) or a request to render the UI.  
If the queue is empty, the main thread is just looping and wait.  
When an event arrives to that queue, the main thread should be ready to pick up and process.  
If another new event arrives while main thread is busy processing, we suffer a blocking, which is
called bad user experience.

So main thread should process very light weight works. All heavy works should be shifted to background threads.

## Single thread with asynchronous method

In this method, there is only one thread in application.  
This single thread can be called main thread or UI thread too, because it acts the same as above.

This is the method that JS engine in browsers and NodeJS implement.

But how about those heavy works? How can a single thread handle all of them without blocking the UI?

Turns out there are 2 main types of heavy work which we can deal with separately:

- I/O-bound operations: which relate to networking (e.g. requests to server), or file system accessing(e.g. read/write to file system)
- CPU-bound operations: which relate to computation (e.g. arithmetic calculation on large dataset, image processing)

### IO operations

For the IO operations, turns out it's simple: application just shifts the heavy work to the system (OS or engine) which host the app. Take a look at this pseudo JS code:

```js
var url = 'google.com'
var callback = function(response) { console.log(response) }

http.get(url, callback)
```

`http` is any http client. When calling `http.get()`, we pass 2 arguments:

- `url`: is the target we want to fetch data from (google.com)
- `callback`: is the function that we want to do after the data is fetched

In human language: "Hey http, fetch data from google.com, and when you finished, call `callback` function with the response you have"

Here, the app uses `http.get()` to tell underlying JS engine (or OS) 2 things: the work and the callback.  
Notice that this call to `http.get()` is very light weight. You can think it's just registering and return. The app will immediately continue to the next line of code.

Then the engine performs actual IO (network) operation. It can spawn new thread, using interrupt mechanism or whatever. But the app just don't care.
When work finished, the engine calls the registered `callback`, passing along with the response.

To summary, single thread app can handle all IO operations asynchronously, with the help of underlying system. App doesn't need to create new worker thread to process any IO operation.

### CPU-bound operations

But CPU-bound operations are not supported by engine or OS, because it's too customized, depends on the application logic. App need to create new worker thread to process heavy CPU computation operations. So, no single thread anymore.

# Asynchronous in Javascript

JS is single thread by nature.

At the beginning, we write code like this:

```js
http.get('abc.com/login', function(res) {
    console.log('Logged in')
})
```

Then `promise` is invented:

```js
http.get('abc.com/login').then(function(res) {
    console.log('Logged in')
})
```

And then, `async` and `await`

```js
await http.get('abc.com/login')
console.log('Logged in')
```

# Asynchronous in C#


https://docs.microsoft.com/en-us/dotnet/csharp/async

https://docs.microsoft.com/en-us/dotnet/standard/async-in-depth

https://stackoverflow.com/questions/33821679/async-await-different-thread-id
https://blogs.msdn.microsoft.com/pfxteam/2012/04/12/asyncawait-faq/