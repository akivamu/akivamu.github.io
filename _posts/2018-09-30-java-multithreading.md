---
layout: post
title: JAVA multi threading
tags:
- JAVA
---

# `synchronized` keyword

## `synchronized` method

Suppose we have a kitchen with 1 oven:

```
public class Kitchen {
    private Object oven;

    public synchronized void makePie(String who) {
        // Pretend using `oven`
        System.out.println(who + " is using oven");
        // It takes 2s to make a pie
        Thread.sleep(2000);
    }
}
```

Alice and Bob are trying to make pie at the same time:
```
Kitchen kitchen = new Kitchen();

System.out.println("Alice tries to make a pie");
new Thread(() -> {
    kitchen.makePie("Alice");
    System.out.println("Alice done making pie");
}).start();

System.out.println("Bob tries to make a pie too");
new Thread(() -> {
    kitchen.makePie("Bob");
    System.out.println("Bob done making pie");
}).start();
```

We will see the console output:
```
Alice tries to make a pie
Bob tries to make a pie too
Alice is using oven
Alice done making pie
Bob is using oven
Bob done making pie
```

Simple here: Bob requested `makePie` after Alice, he has to wait until Alice done.
Because there is only 1 oven in the kitchen.

**Note:** I put the object `Object oven` just for demonstrate the concept of resource here.
It's useless in this code, for now.

The `synchronized` method `makePie()` prevents 2nd request (by Bob) until the method returns.

### Multiple `synchronized` methods

We add a coffee machine to kitchen:

```
public class Kitchen {
    private Object oven;
    private Object coffeeMachine;

    public synchronized void makePie(String who) {
        System.out.println(who + " is using oven");
        Thread.sleep(2000);
    }

    public synchronized void makeCoffee(String who) {
        System.out.println(who + " is using coffee machine");
        Thread.sleep(1000);
    }
}
```

Now, Alice want to make a pie.  
At the same time, Bob want to make a coffee:
```
Kitchen kitchen = new Kitchen();

System.out.println("Alice trying to make a pie");
new Thread(() -> {
    kitchen.makePie("Alice");
    System.out.println("Alice done making pie");
}).start();

System.out.println("Bob trying to make a coffee");
new Thread(() -> {
    kitchen.makeCoffee("Bob");
    System.out.println("Bob done making a coffee");
}).start();
```
Can both Alice and Bob do the work without waiting for each other?  
NO! The console output is:
```
Alice trying to make a pie
Bob trying to make a coffee
Alice is using oven
Alice done making pie
Bob is using coffee machine
Bob done making pie
```

`synchronized` methods locks the whole object (`Kitchen`), not just `oven` or `coffeeMachine`.

**Key note: A synchronized instance method is synchronized on the instance (object) owning the method.**

### Multiple instances of `Kitchen`

If we have 2 kitchen instances like this:
```
Kitchen kitchen1;
Kitchen kitchen2;
```

Then these 2 calls can be executed simultaneously:

```
kitchen1.makePie()
kitchen2.makeCoffee()
```
It's common sense and no thing interested here.


## `synchronized` static method

If we make `synchronized` methods to be `static`, like this:
```
public class Kitchen {
    private Object oven;
    private Object coffeeMachine;

    public static synchronized void makePie(String who) {
        System.out.println(who + " is using oven");
        Thread.sleep(2000);
    }

    public static synchronized void makeCoffee(String who) {
        System.out.println(who + " is using coffee machine");
        Thread.sleep(1000);
    }
}
```
Then these 2 static calls are synchronized, and one must wait for each other to finish:

```
Kitchen.makePie()
Kitchen.makeCoffee()
```

## `synchronized` block

Instead of mark methods as `synchronized`, we make a inner code block `synchronized`:
```
public class Kitchen {
    public void makePie(String who) {
        System.out.println(who + " is trying to use oven");

        synchronized(this) {
            System.out.println(who + " is using oven");
            Thread.sleep(2000);
        }
    }
}
```
Using it:
```
Kitchen kitchen = new Kitchen();

new Thread(() -> {
    kitchen.makePie("Alice");
    System.out.println("Alice done making pie");
}).start();

new Thread(() -> {
    kitchen.makePie("Bob");
    System.out.println("Bob done making pie");
}).start();
```

Output console:
```
Alice is trying to use oven
Alice is using oven
Bob is trying to use oven
Alice done making pie
Bob is using oven
Bob done making pie
```

We can see the method `makePie` can be entered simultaneously without being blocked.  
Only this code is blocked:
```
synchronized(this) {
    System.out.println(who + " is using oven");
    Thread.sleep(2000);
}
```

# `Thread.join()`

Suppose we're building web server which uses database.  
Before starting web server, we must ensure database is initialized.  
The initialization is perform in this thread:
```
public class InitDatabase extends Thread {
    @Override
    public void run() {
        System.out.println("Database: I'm booting up");
        Thread.sleep(500);
        System.out.println("Database: I am ready");
    }
}
```

We use `join()` to wait for thread finished (or die)
```
System.out.println("Me: Initializing database");
InitDatabase initDatabase = new InitDatabase();
initDatabase.start();

System.out.println("Me: Waiting database ready before starting Web");
initDatabase.join();

System.out.println("Me: OK. starting web");
```
Here `initDatabase.join()` will pause **current thread**, and wait for the thread `InitDatabase` finished.

# `Object.wait()` and `Object.notify()`

Let's make a situation about the Producer/Consumer problem:

- Consumer: me. I want to eat a pizza.
- Producer: Pizza delivery guy.
- Pizza Guy produces a pizza for me to consume.
- I have to wait for Pizza Guy to deliver a pizza into my house, before I can eat.

## Use `synchronized` method

We can make an implementation like this:
```
class MyHouse {
    private volatile boolean hasPizza = false;

    public synchronized void eatPizza() {
        System.out.println("Me: Finding pizza...");
        while (!hasPizza) ; // Waiting for pizza
        System.out.println("Me: Pizza found! Eating...");
    }

    public synchronized void deliverPizza() {
        System.out.println("Pizza guy: At the door!");
        hasPizza = true; // Waiting for pizza
        System.out.println("Pizza guy: Pizza delivered!");
    }
}
```
Since `hasPizza` is shared resource, it's reasonable to synchronize it here.  
But this implementation is bad, causes dead lock like this code:
```
MyHouse myHouse = new MyHouse();

// Me trying to eat pizza first
new Thread(() -> {
    myHouse.eatPizza();
}).start();

// But Pizza Guy is still on his way. No pizza in the house yet
new Thread(() -> {
    System.out.println("Pizza guy: On my way...");
    Thread.sleep(2000); // Takes 2s on bike
    myHouse.deliverPizza(); // DEAD LOCK!!!
}).start();
```
Console output:
```
Me: Finding pizza...
Pizza guy: On my way...
```
You can see when calling `eatPizza` the object is locked in `while` loop.  
When call `deliverPizza`, it has to wait for object to be released (`synchronized` method).  
So, `hasPizza = true` is never executed. Both threads is in DEAD LOCK situation.

## Use `wait()` and `notify()`

Make a new implementation:
```
class MyHouse {
    private boolean hasPizza = false;

    public synchronized void eatPizza() {
        System.out.println("Me: Finding pizza...");
        if (!hasPizza) wait(); // NEW
        System.out.println("Me: Pizza found! Eating...");
    }

    public synchronized void deliverPizza() {
        System.out.println("Pizza guy: At the door!");
        this.hasPizza = true;
        notifyAll(); // NEW
        System.out.println("Pizza guy: Pizza delivered!");
        Thread.sleep(1000);
        System.out.println("Pizza guy: Left the house");
    }
}
```
With the same code as above case:
```
MyHouse myHouse = new MyHouse();

// Me trying to eat pizza first
new Thread(() -> {
    myHouse.eatPizza();
}).start();

// But Pizza Guy is still on his way. No pizza in the house yet
new Thread(() -> {
    System.out.println("Pizza guy: On my way...");
    Thread.sleep(2000); // Takes 2s on bike
    myHouse.deliverPizza(); // NO dead lock here
}).start();
```
Console output:
```
Me: Finding pizza...
Pizza guy: On my way...
Pizza guy: At the door!
Pizza guy: Pizza delivered!
Pizza guy: Left the house
Me: Pizza found! Eating...
```
No dead lock, still 2 `synchronized` methods. Interesting?

The key difference here is: calling to `wait()` will **release the lock** to `myHouse` immediately (without return from
method), so that other threads can enter `synchronized` methods.

Look at the line `if (!hasPizza) wait()` and [java doc](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#wait()),
we can see `wait()` method **releases ownership of this monitor and waits until another thread notifies**.

The steps:

- ThreadA (i.e. "me" in this case) takes the lock on `myHouse`, and enters `eatPizza()`
- Thread A executes `wait()` method, it will releases lock on `myHouse` and wait. Now ThreadA is pausing.
- ThreadB (i.e. pizza guy) can enter `deliverPizza()` method because lock is released.
- ThreadB takes the lock on `myHouse`.
- ThreadB then executes `notifyAll()`, which wakes up ThreadA.
- ThreadA is waked up, but still can't continue flow, because ThreadB is still holding the lock.
- ThreadB finishes the rest, return and release the lock
- ThreadA continues the rest

But the story ins't end here.

### DO NOT: `if (!hasPizza) wait()`

Instead, we use `while (!hasPizza) wait()`. It's because of **spurious wakeup**.

In simple word, using `if (!hasPizza) wait()`:

- ThreadA executes `wait()`, go to sleep and expect some one will `notify` in the right time.
- But one asshole guy - OS, suddenly makes a fake wake up call. Why? Google it.
- ThreadA wakes up, exits `wait()` and exits `if` block. Now it assumes `hasPizza == true` and do eating ... the paper!!!

That is why we **use loop:** `while (!hasPizza) wait()`  
After a fake wake up, ThreadA is still in while loop, it checks `hasPizza` and `wait()` again.
Safe!

### `wait` and `notify` must be in `synchronized`

[Detail answer here](https://stackoverflow.com/a/2779674/6445037)

# `volatile` keyword

This time, no `synchronized`, no `wait` or `notify`.  
Just use `if` to deal with multi threads locking:
```
class StopThread extends Thread {
    private boolean stopped = false;

    @Override
    public void run() {
        System.out.println("Thread is running");
        while (!stopped) {
            // Working...
        }
        System.out.println("Thread is finished");
    }

    void stopThread() {
        stopped = true;
        System.out.println("Someone stops the thread");
    }
}
```
Use it:
```
StopThread resource = new StopThread();
resource.start();

Thread.sleep(100);
resource.stopThread();
```
Result:
```
Thread is running
Someone stops the thread
```
The thread is not stopped. It stuck forever at `while (!stopped)`. Even after `Someone stops the thread` 
(and set `stopped = true`), the loop is still running!  
Why? It's because the variable `stopped` in while loop is just a cached value, isn't updated.
[Read more here](http://tutorials.jenkov.com/java-concurrency/volatile.html)

So that, we use `volatile`:
> “… the volatile modifier guarantees that any thread that reads a field will see the most recently written value.” - Josh Bloch

And:
> By declaring the variable volatile: all writes to the variable will be written back to main memory immediately. Also, all reads of the variable will be read directly from main memory.

Edit the code: `private volatile boolean stopped = false;` will fix the problem.

# Todo

[This series](http://tutorials.jenkov.com/java-concurrency/index.html) seems good, take time to read it.