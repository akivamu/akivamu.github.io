---
layout: post
title: JavaScript sparse array by example
tags:
- JavaScript
---

## JavaScript array is still an object

{% highlight js %}
var arr = [] // declare new array

// Array has properties too
arr.test1 = 111
arr['test1'] = 111 // prop can also be accessed by square bracket
arr.test1 == arr['test1'] // true

arr['test2'] = 111
arr.test1 == arr.test2 // true
{% endhighlight %}

## Only valid natural numeric properties are counted as index

Valid index range: [0,2<sup>32-1</sup>)  
If we use invalid index, then JS treats array as normal object, not array nature.
{% highlight js %}
arr.test1 = 111
arr['test2'] = 111
arr.length == 0 // true - 'test1' and 'test2' are not numeric, not counted as array elements

arr['0'] = 111 // make first element, at index 0
arr.length == 1 // true - '0' is number, so now array has 1 element

arr['-1'] = 111
arr[-1] = 111
arr.length == 1 // true - '-1' is number but out of valid range, so not counted as an array element
arr['-1'] // will print 111, '-1' is not counted as index, but still a property and can be accessed as normal
arr[-1] // will print 111 also

arr[4294967296] = 222
arr.length == 1 // true - '4294967296' is number but out of valid range, so not counted as array elements
arr[4294967296] // will print 222, '4294967296' is not counted as index, but still a property and can be accessed as normal

arr.0 = 111 // Syntax error: must use square bracket to access numeric properties
{% endhighlight %}

## Add element at arbitrary index
Array will expand to fit largest index number.
{% highlight js %}
arr['2'] = 222 // let skip index 1, make element at index 2
arr.length == 3 // true - now array has 3 elements
arr // will print [111,,222] note that there is a hole in middle
{% endhighlight %}

### Force change `length` property
{% highlight js %}
arr.length = 5 // manually increase length from 3 to 5
arr // will print [111,,222,,] 2 holes are added at the end

arr.length = 1 // manually reduce length from 5 to 1
arr // will print [111] all elements beyond length are removed
{% endhighlight %}

## Array with holes is called sparse array
{% highlight js %}
var sparseArr = []

sparseArr[0] = 0
sparseArr[3] = 3

sparseArr // will print [0,,,3] there are 2 holes in middle

// another method to create array with holes
var sparseArr = [0,,,3]
sparseArr // will print [0,,,3] there are 2 holes in middle

// Holes are also counted in array length
sparseArr.length // will print 4, included 2 holes
{% endhighlight %}

## Array without holes is called dense array
{% highlight js %}
var denseArr = []
denseArr[0] = 0
denseArr[1] = undefined
denseArr[2] = undefined
denseArr[3] = 3

// another method to create array without holes
var denseArr = [0,undefined,undefined,3]
{% endhighlight %}

## Sparse array vs dense array
{% highlight js %}
var sparse = [0,,2]

var dense = [0,undefined,2]
{% endhighlight %}

### for var
{% highlight js %}
for (var i=0; i<sparse.length; i++) console.log(sparse[i]);
// both sparse and dense have the same output
// 0
// undefined
// 2
{% endhighlight %}

### forEach
{% highlight js %}
sparse.forEach(function (x) { console.log(x) });
// will print
// 0
// 2

dense.forEach(function (x) { console.log(x) });
// will print
// 0
// undefined
// 2
{% endhighlight %}

### for in
{% highlight js %}
for (var key in sparse) { console.log(key) }
// will print
// 0
// 2

// Now add non-index property
sparse['test'] = 'test';
for (var key in sparse) { console.log(key) }
// will print
// 0
// 2
// test
{% endhighlight %}

So avoid use `for in` to loop over array, because it includes non-index property also.