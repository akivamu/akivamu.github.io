---
layout: post
title: JavaScript apply(), call() and bind()
tags:
- JavaScript
---

Suppose we have a function:
{% highlight js %}
function selfGreeting() {
    console.log('Hello from ' + this.name)
}
{% endhighlight %}

We call that function in normal way:
{% highlight js %}
selfGreeting()
{% endhighlight %}

Would result: `Hello from `

Simple, because in the context of calling, `name` or `this.name` is undefined.

## Now we use `call()`
{% highlight js %}
var person = {name: 'Bob'}

selfGreeting.call(person)
{% endhighlight %}

Result is: `Hello from Bob`

Here we pass the `person` variable to `call()`, make it becomes the context of `selfGreeting` function. Now in that
function scope, `this` variable is `person` variable, and `this.name` has value `Bob`.

From [mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call){:rel="nofollow" target="_blank"}:  
> The `call()` method calls a function with a given `this` value and arguments provided individually.

## We can also use `apply()`
{% highlight js %}
selfGreeting.apply(person)
{% endhighlight %}

Result is: `Hello from Bob`

In short, when we pass an object to `call()` or `apply()` to invoke a function, the `this` variable will be replaced
by that object.

## Call it later
These 2 cases above are for immediately invoke a function.  
Now, we don't want to invoke the function now, instead, pass it as a callback for others to call later, like this one:
{% highlight js %}
function waitAndDo(callback) {
    setTimeout(function () {
        callback();
    }, 1000)
}

waitAndDo(selfGreeting)
{% endhighlight %}

Of course it will wait 1000ms and print: `Hello from `  
`this.name` is still undefined.

Fixing it: we can rewrite code to use `call()` or `apply()` methods to pass our context.
{% highlight js %}
function waitAndDo(callback, context) {
    setTimeout(function () {
        callback.call(context)
    }, 1000)
}

var person = {name: 'Bob'}

waitAndDo(selfGreeting, person)
{% endhighlight %}

Result is: `Hello from Bob`

### Or we can use `bind()` method for more elegant
{% highlight js %}
function waitAndDo(callback) {
    setTimeout(callback, 1000)
}

var person = {name: 'Bob'}

// Use bind() to create new function and attach context to it
var newFunction = selfGreeting.bind(person)

waitAndDo(newFunction)
{% endhighlight %}

Result is: `Hello from Bob`

## Summary
* Similarity: `apply`, `call` or `bind` are used for passing the context to function execution (replace `this` variable).
* `apply` and `call` are used to call function immediately.
* `bind` is used to create a new function with context attached. Then this new function can be passed around and
called later with that context.
* You can also pass arguments other than context to `apply`, `call` and `bind`.
* Detail signature of [apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply), [call](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) and [bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)