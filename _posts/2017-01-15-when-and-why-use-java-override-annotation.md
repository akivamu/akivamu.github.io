---
layout: post
title: When and why use Java @Override annotation
date: '2017-01-15T17:58:00.000+07:00'
tags:
- Java
---

## When should we use `@Override` annotation?
* When you want to override a method of parent class
* When you want to implement a method of an interface

## And if we forgot to use `@Override` annotation?
It's just fine, no error happens. But there will be potential unexpected behaviours due to human mistakes.

## So why should we use `@Override` annotation?
* For code readability.
* To take advantage of compiler error checking.
* If Parent class changed, we can immediately see error when compiling.

Check this example: suppose we have a class Vehicle:
{% highlight java %}
class Vehicle {
  void hello() {
    System.out.print("Hello from Vehicle");
  }
}
{% endhighlight %}

This is case when we don't use `@Override` annotation:
{% highlight java %}
class Car extends Vehicle {
  // Misspelling: No error at compile time
  void halo() {
    System.out.print("Hello from Car");
  }
}
{% endhighlight %}

This code is wrong: we intend to override method `hello`, but we misspelling it, into `halo`. There is no error at
compile time, but only when run time. Let's run a test to confirm:
{% highlight java %}
class Test {
  void main(String[] args) {
    Car car = new Car();
    car.hello(); // Will print "Hello from Vehicle"
  }
}
{% endhighlight %}

This type of error is bad, because it leaves potential bug, until late phase like test or even worse, production
phase. The best practice is to avoid it, by using `@Override` annotation, let's rewrite the code:
{% highlight java %}
class Car extends Vehicle {

  // Compile error: override non existed method
  @Override
  void halo() {

  }

  // Compile error: override non existed method
  // Correct method name, wrong method param
  @Override
  void halo(int param) {

  }
}
{% endhighlight %}

The error can be detected asap, at compile time!

So it's best practice to use `@Override` annotation whenever:
* You want to override a method of parent class
* You want to implement a method of an interface