---
layout: post
title: Big O notation overview by example
date: '2017-02-02T20:42:00.000+07:00'
tags:
- Algorithm
---

[This](https://rob-bell.net/2009/06/a-beginners-guide-to-big-o-notation/){:rel="nofollow" target="_blank"} is great article of basic things about
big O notation. Big O notation is used for describing the complexity of algorithm, or the cost of doing something in
worst case. Just want to sum up here.

## O(1) - constant cost
Doing something always costs the same constant amount of effort, no matter how difference input is.
{% highlight java %}
bool IsFirstElementNull(IList<string> elements)
{
    return elements[0] == null;
}
{% endhighlight %}

## O(N) - linear cost
Cost of algorithm linearly depends on size of input.
{% highlight java %}
bool ContainsValue(IList<string> elements, string value)
{
    foreach (var element in elements)
    {
        if (element == value) return true;
    }

    return false;
}
{% endhighlight %}
In the best case, this algorithm takes 1 effort (comparison) to return result.  
In the worst case, this algorithm takes n effort (length of array) to return result.  
So it is O(N).

## O(N<sup>2</sup>)
{% highlight java %}
bool ContainsDuplicates(IList<string> elements)
{
    for (var outer = 0; outer < elements.Count; outer++)
    {
        for (var inner = 0; inner < elements.Count; inner++)
        {
            // Don't compare with self
            if (outer == inner) continue;

            if (elements[outer] == elements[inner]) return true;
        }
    }

    return false;
}
{% endhighlight %}
In the worst case, this algorithm takes n * n effort to return result.  
It takes n iterations to go through all items in outer loop. For each iteration, need again go through all items in
inner loop. It take n * n steps. So it is O(N<sup>2</sup>).  
Simple words:
* 2 layers of loop: O(N<sup>2</sup>)
* 3 layers of loop: O(N<sup>3</sup>)
* 4 layers of loop: O(N<sup>4</sup>)
* and so on

## O(2<sup>n</sup>)
{% highlight java %}
int test(int number)
{
    if (number == 0) return number;

    return test(number - 1) + test(number - 1);
}
{% endhighlight %}

Cost will doubles each time we increase size of data by 1. Because each step will call 2 function recursively:
* number = 0: 1 step (return expression)
* number = 1: 2 steps i.e. 2 function calls test(0)
* number = 2: 4 steps i.e. 2 function calls test(1), each has 2 function calls test(0)
* number = 3: 8 steps i.e. 2 function calls test (2), each has 2 test(1) and each has 2 test(0)

## O(log(n))
Quite hard to explain by word. Let's see this example from [SOF](http://stackoverflow.com/questions/17122807/big-o-ologn-code-example){:rel="nofollow" target="_blank"}:
{% highlight java %}
while (n > 0) {
   n/=2; 
}
{% endhighlight %}

Demonstration

Iteration|n
---|---
0|n
1|n/2
2|n/4
...|...
k|n/2<sup>k</sup>

2<sup>k</sup> = n -> k = log(n)  
So we have O(log(n))