---
layout: post
title: Java nested class
tags:
- Java
---

# Definition
Nested class is declaring a class inside another class. There are 2 types:
  - Static nested class
  - Non-static nested class, divided into 3 types:
    - Inner class
    - Method local inner class
    - Anonymous

# Static nested class

```
class Outside {
    static class Inside {

    }
}
```
**Key note:** If access modifier allowed, we can create instance of `Inside` class independently, without any relation
to `Outside` class. Like this:
```
Outside.Inner instance = new Outside.Inside();
```
or import it:
```
import Outside.Inside;
...
Inside instance = new Inside();
```

# Non-static nested class
## Inner class
Declare it without `static` keyword:
```
class Outside {
    class Inside {

    }
}
```
**Key note:** Only instance of `Outside` class can create instance of `Inside` class. 
```
// Create instance of outer class first
Outside outer = new Outside();

// Use it to create instance of inner class
Outside.Inside inner = outer.new Inside();
```
## Method local inner class
We can declare class inside a method, making it accessible only within that method.
```
class Outside {
    void test() {
        class Inside {
            
        }
        
        Inside instance = new Inside();
    }
    
    void anotherTest() {
        Inside instance = new Inside(); // compile error
    }
}
```

## Anonymous inner class
We can also declare a class without name, mostly when override a method of class or interface.
```
class Template {
    void sampleMethod() {
        println("Template");
    }
}

class Outside {
    void test() {

        // Declare & create instance at the same time
        Template instance = new Template() {
            @Override
            void sampleMethod() {
                println("Anonymous");
            }
        }
        
        instance.sampleMethod(); // will print: Anonymous
    }
}
```
We create new class that override method `sampleMethod` without naming it.

# Nested class access modifier
Of course nested class has to follow access modifier rule, declare nested classes like this will make them can't be
seen outside of `Outer`:
```
class Outer {
    private class PrivateInner {

    }
    
    private static class PrivateNested {

    }
}
```
Now we can only create instance of `PrivateNested` and `PrivateInner` class inside the `Outer` class.

## Nested class member access modifier
Outer class and nested class can see all each other properties and methods, regardless of access modifier.
```
public class Outer {
    private int a;

    private void test() {
        Inner inner = new Inner();
        inner.b = 1; // access nested class private property
        a = inner.b;
    }

    public class Inner {
        private int b;

        private void test() {
            b = a; // access outer class private property
        }
    }
}
```