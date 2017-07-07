---
layout: post
title: Java varargs
tags:
- Java
---

# Definition
This code declare a function that can take arbitrary number of parameters. The parameters must be the same type `int`.
```
void sampleFunction(int... args) {
    System.out.print(args.length);
    
    for (int i = 0; i < args.length; i++) {
        System.out.print(args[i]);
    }
}
```
It's possible to mix `varargs` with other parameter types, like this:
```
void sampleMixingFunction(String name, Date date, int... args) {
    ...
}
```
The `varargs` must be the last parameters in function signature.  
It's not allowed to declare multiple `varargs` in one function.

# Usage

## With any number of parameters
You can pass any number of parameters to function, but must be the same type.
```
void test() {
    sampleFunction(1);         // will print: 11
    sampleFunction(1,2);       // will print: 212
    sampleFunction(1,2,3,4,5); // will print: 512345
}
```

## With none parameter
You can also pass none parameter too. In this case, the array length is zero.
```
void test() {
    sampleFunction(); // will print: 0
}
```
**Note:** In some case, it may lead to misusing when user accidentally calls function with none parameter. To prevent
it, we don't allow empty parameters calling by making first parameter mandatory:
```
void sampleFunction(int mandatoryArg, int... args) {
    ...
}
```
By this, calling to `sampleFunction` with none parameter will result compile time error.

## With single `null` parameter
If you pass `null`, the array will be `null`. Thus, access to `args.length` will result `NullPointerException`.
```
void test() {
    sampleFunction(null); // will throw NullPointerException
}
```
