---
layout: post
title: Inversion Of Control
tags:
- Software Design
---

## Concepts

Inversion Of Control (aka IoC) can be describe as **Hollywood Principle: Don't call us, we'll call you"**

Let's see who are we and who are you?

## Examples

### Non IoC program

```C#
class Program {

  void main() {
    Console.Writeline("Input your name: ");
    string name = Console.Readline();

    Console.Writeline("Hello " + name);
  }
}
```

This is tradition program, where the developer controls whole flow of processing.  
We prompt user to input, and wait for input, then print the hello.

### IoC program

Let's assume there is a framework that defines the generic flow of processing.  
This framework is provided, and developer don't have control over the flow.  

```C#
class CoolFramework {
  EventListener you;

  CoolFramework(EventListener you) {
    this.you = you;
  }

  void main() {
    Console.Writeline("Input your name: ");
    string name = Console.Readline();

    you.onUserEnteredName(name);
  }
}

interface EventListener {
  void onUserEnteredName(string name);
}
```

Then, developer only has to write this code:

```C#
class YourCode : EventListener {
  void onUserEnteredName(string name) {
    Console.Writeline("Hello " + name);
  }
}
```

And of course, there is wiring code that wire up developer code to the framework:

```C#
class Program {

  void main() {
    EventListener you = new YourCode();
    CoolFramework we = new CoolFramework(you);

    we.main();
  }
}
```

We can see our code doesn't control the flow, the framework does.  
Hence, we (`YourCode`) don't call anything specific to the flow, the framework `CoolFramework` calls us
(via `EventListener.onUserEnteredName`)

Hence, inverse the control

## Why Inverse Of Control

- Decouple the execution (call `EventListener.onUserEnteredName`) from the implementation (`Console.Writeline("Hello " + name)`)
- Extensibility, focus the module (`YourCode`) on the task (`onUserEnteredName`)
- Safe to replace modules (side effects that could affect the flow)
