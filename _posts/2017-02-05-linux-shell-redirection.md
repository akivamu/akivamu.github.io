---
layout: post
title: Linux shell redirection
tags:
- Linux
---

Even though I googled and used this many time, I always forgot about all these: `&2>1`, `1>2&`, `1>&2`. So I hope that
writing it down myself can help to remember their rule.

## Linux file descriptor
- 0: stdin
- 1: stdout
- 2: stderr

## Redirect to a file name

### Redirect output to a file named `1`
{% highlight shell %}
echo test>1 # test
echo test >1 # test
echo test > 1 # test
{% endhighlight %}

### Redirect stdout/stderr to a file named `1`
{% highlight shell %}
# redirect stdout
echo test 1>1 # test
echo test 1> 1 # test
echo test 1 > 1 # test 1

# redirect stderr
echo test 2>1 # test
{% endhighlight %}

### Redirect both stdout and stderr to a file named `1`
{% highlight shell %}
echo test &>1 # redirect stdout + stderr to file named `1`
{% endhighlight %}

## Redirect to a file descriptor
{% highlight shell %}
echo test 2>&1 # redirect stderr to stdout
echo test 2> &1 # shell error (space character before &1)

echo test >&2 # redirect stdout to stderr
echo test 1>&2 # redirect stdout to stderr
{% endhighlight %}

## stderr and Pipes
This won't work
{% highlight shell %}
$ grep hosts /etc | grep 'Is a directory' > result.txt
grep: /etc: Is a directory
$ cat result.txt

{% endhighlight %}
If we want to pipe the stderr, have to redirect stderr to stdout first
{% highlight shell %}
$ grep hosts /etc 2>&1 | grep 'Is a directory' > result.txt
$ cat result.txt
grep: /etc: Is a directory
{% endhighlight %}

## Reference
[http://www.jstorimer.com/blogs/workingwithcode/7766119-when-to-use-stderr-instead-of-stdout](http://www.jstorimer.com/blogs/workingwithcode/7766119-when-to-use-stderr-instead-of-stdout){:rel="nofollow" target="_blank"}  
[http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html){:rel="nofollow" target="_blank"}