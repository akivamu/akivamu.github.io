---
layout: post
title: First-class function in JavaScript and Java
tags:
- JavaScript
- Java
---

## What is first-class function?
* Can be named by variables.
* Can be passed as arguments to procedures.
* Can be returned as the results of procedures.
* Can be included in data structures.

## Functions are first class objects in JavaScript
With reference from [this post](http://helephant.com/2008/08/19/functions-are-first-class-objects-in-javascript/),
functions are first class objects in JavaScript:
* A function is an instance of the Object type
* A function can have properties and has a link back to its constructor method
* You can store the function in a variable
* You can pass the function as a parameter to another function
* You can return the function from a function

{% highlight js %}
function hello() {
  return 'hello'
}

// Function is instance of the Object type
hello instanceof Object // true

// Function can has properties and has a link back to its constructor method
hello.personName = 'Bob'

console.log(hello.personName)
console.log(hello.constructor)

// You can store the function in a variable
var storedFunction = hello
storedFunction() // same as hello()

// You can pass the function as a parameter to another function
function saySomething(functionAsParameter) {
  console.log('I say: ' + functionAsParameter());
}

saySomething(hello);  // will print 'I say: hello'

// You can return the function from a function
function getSecretFunction(){
  return hello
}

var secretFunction = getSecretFunction()
secretFunction()
{% endhighlight %}

## First class objects in Java
In Java these are first class objects:
* Primitive values: int, double, long, short, byte, char...
* Objects: new Integer(1), new ArrayList(), new Person()...

Classes and methods are second class objects...until when Java 8 was introduced:
{% highlight java %}
public class FilterTool {
    public List<String> filter(List<String> list, Predicate<String> predicate) {
        List<String> result = new ArrayList<>();
        for (String string : list) {
            if (predicate.test(string)) {
                result.add(string);
            }
        }
        return result;
    }

    public static boolean moreThan2Characters(String word) {
        return word.length() > 2;
    }
}
{% endhighlight %}

We will try to pass a function as parameter to other function
{% highlight java %}
@Test
public void test() {
    FilterTool filterTool = new FilterTool();
    List<String> originalList = new ArrayList<>();
    originalList.add("a");
    originalList.add("aa");
    originalList.add("aaa");
    originalList.add("aaaa");

    // Pass moreThan2Characters as parameter
    Assert.assertEquals(2, filterTool.filter(originalList, FilterTool::moreThan2Characters).size());
}
{% endhighlight %}