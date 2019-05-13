---
layout: post
title: .NET Async programming
tags:
- DotNet
---

.NET has almost similar mechanism as Javascript, but with important difference: multiple threading.

## Heavy computation code in separate thread

.NET supports multithread, means we can achieve non-blocking async even with CPU-bound operation. (Use
`Task.Run()` to run CPU-bound async code in a separate thread)

General information is in official docs:

- [https://docs.microsoft.com/en-us/dotnet/csharp/async](https://docs.microsoft.com/en-us/dotnet/csharp/async)
- [https://docs.microsoft.com/en-us/dotnet/standard/async-in-depth](https://docs.microsoft.com/en-us/dotnet/standard/async-in-depth)
- [FAQ about async-await](https://blogs.msdn.microsoft.com/pfxteam/2012/04/12/asyncawait-faq/)

## Which thread should the completed code run on

Consider this code:

```cs
async Task FooAsync()
{
    Console.WriteLine("Before: ThreadID = " + Thread.CurrentThread.ManagedThreadId);
    await AnyAsyncMethod();
    Console.WriteLine("After: ThreadID = " + Thread.CurrentThread.ManagedThreadId);
}
```

What should be printed?

Generally, async await "tries to bring you back to where you were", means it "tries" to make those `Console.WriteLine` run on same thread.

But it depends on the "scheduler": [SynchronizationContext and TaskScheduler](https://devblogs.microsoft.com/pfxteam/await-synchronizationcontext-and-console-apps/)  
Read above link for more detail. In summary:

In the "contextful" application (like UI, Web), runtime will try to capture the context, then after async code completed,
it can resume in that context. e.g. posting the remain code lines to UI context, the UI decide to run in UI thread.  
When there is no context captured, the remain code will run on thread pool (without re-enter the context)

In the "contextless" application (like Console), the continuation can be run "anywhere" (include orginal thread)  

### SynchronizationContext

References:

- [Parallel Computing - It's All About the SynchronizationContext](https://msdn.microsoft.com/en-us/magazine/gg598924.aspx)

### Beware of async multithreading - race condition

Since the async operation in .NET might happen in parallel (multithread), pay attention for race condition.

## Make async become sync

Every async is represented by `Task`.

When you `await` on it, control is yielded to caller - normal async code behavior.

When you `task.Result` or `task.GetAwaiter().GetResult()`, the control is blocked, waiting for result.  
[Here is the difference between them](https://stackoverflow.com/questions/17284517), only when task failed:

- `Task.Result` will throw an AggregateException
- `Task.GetAwaiter().GetResult()` just throw the exception caused directl

### Deadlock in contextful async

You should be careful when making async code become blocking like above.  
Combining with contextful will create deadlock.

Let's say we have an UI app, which is contextful:

```cs
async Task<string> FooAsync()
{
    return await AnyAsyncMethod();
}

// Run on UI thread
void OnBtnClicked()
{
    text = FooAsync().Result
}
```

The line `text = FooAsync().Result` will start async code and wait for result, it blocks the UI thread.  
When starting the async code, the context is captured (to resume continuation in original context)

Later, when async operation completed, it need to run on the original context, which is current blocked.

So, deadlock.

You can prevent it by telling the continuation to NOT resume in original context, by `ConfigAwait(false)`

```cs
async Task<string> FooAsync()
{
    return await AnyAsyncMethod().ConfigureAwait(false);
}
```

## Make sync become async

You can make sync operation become async (kind of), mostly just to be able to `await` it.  
You wrap it in `Task.Run()`

```cs
// CPU expensive sync code
void HeavyCalculation()
{
    // ...
}

// Make to async
public Task HeavyCalculationAsync()
{
    return Task.Run(() => HeavyCalculation());
}

// So you can await it
await HeavyCalculationAsync()
```

Basically you are just offload operation into background thread.

Read: [Should I expose asynchronous wrappers for synchronous methods?](https://devblogs.microsoft.com/pfxteam/should-i-expose-asynchronous-wrappers-for-synchronous-methods/)

In short:

- For scalability: Don't. Since it still cost same CPU amount to perform, and spawning thread is costly.
- For offloading: Ok. Increate responsiveness
