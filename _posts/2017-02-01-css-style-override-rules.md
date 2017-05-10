---
layout: post
title: CSS style override rules
date: '2017-02-01T19:11:00.000+07:00'
tags:
- CSS
- Frontend
---

This article will recap how one CSS rule override another CSS rule by example.

We have a simple HTML like this:
{% highlight html%}
<style>
    body {
        color: green;
    }
</style>

<div>Hello World!</div>
{% endhighlight %}
Result is: <span style="color: green;">Hello World!</span>

## Rule 1: More specific rule will win
Let's create a CSS class named `red-text`, and assign to our div:
{% highlight html%}
<style>
    body {
        color: green;
    }

    .red-text {
        color: red;
    }
</style>

<div class="red-text">Hello World!</div>
{% endhighlight %}
Result is: <span style="color: red;">Hello World!</span>  
As you can see, class `red-text` is more specific than body, so it override body style.  
**Important note:** This rule is the **strongest** one. More specific rule will always win.
Below we will discuss rules that have the same level of specific.

## Rule 2: Last rule will win
Let's add another CSS class named `blue-text` below class `red-text`, and add to our div:
{% highlight html%}
<style>
    body {
        color: green;
    }

    .red-text {
        color: red;
    }

    .blue-text {
        color: blue;
    }
</style>

<div class="red-text blue-text">Hello World!</div>
{% endhighlight %}
Result is: <span style="color: blue;">Hello World!</span>  
As you can see, class `blue-text` is declared after `red-text`, so it wins.  
Note that the order is only matter in CSS declaration, not in HTML tag. So these 2 lines are exactly the same:
{% highlight html%}
<div class="red-text blue-text">Hello World!</div>
<div class="blue-text red-text">Hello World!</div>
{% endhighlight %}

## Rule 3: `id` overrides `class`
Let's add `id` to our div, and add CSS rule for that id:
{% highlight html%}
<style>
    body {
        color: green;
    }

    .red-text {
        color: red;
    }

    .blue-text {
        color: blue;
    }

    #greeting {
        color: orange;
    }
</style>

<div class="red-text blue-text" id="greeting">
    Hello World!
</div>
{% endhighlight %}
Result is: <span style="color: orange;">Hello World!</span>  
`id` declarations override `class` declarations, regardless of where they are declared. You can think of `id` is more
specific than `class`.

## Rule 4: In-line style overrides in-file style
Let's add in-line style to our div by using attribute `style`:
{% highlight html%}
<div class="red-text blue-text"
     id="greeting"
     style="color: cyan">
    Hello World!
</div>
{% endhighlight %}
Result is: <span style="color: cyan;">Hello World!</span>  
In-line styles will override all the CSS declarations in your `<style>` tag element or in other CSS file.

## Rule 5: `!important` is strongest one (kind of)
Let's use `!important` rule in our CSS:
{% highlight html%}
<style>
    body {
        color: green;
    }

    .red-text {
        color: red !important;
    }

    .blue-text {
        color: blue;
    }

    #greeting {
        color: orange;
    }
</style>

<div class="red-text blue-text"
     id="greeting"
     style="color: cyan">
    Hello World!
</div>
{% endhighlight %}
Result is: <span style="color: red;">Hello World!</span>  
The rule with `!important` will not be override by other non-`!important` rules.

### What if we have more than one `!important` rules?
Then again, rules from 2 to 4 will be applied among these `!important` rules only. All other non-`!important` rules
will be overridden.  
Note that rule#1 is still the strongest one. `!important` can not override more specific rule.