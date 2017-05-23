---
layout: post
title: Show code snippet with Google Prettify
tags:
- Frontend
- Blogger
---

Recently, I used [SyntaxHighter]({{ site.url }}/how-to-show-code-snippet-in-blogger) to show source code in my blog. It works fine,
but I feel something not very familiar. Just like the source code shown isn't similar to other pages, like Github. I
decided to change to [Google Prettify](https://github.com/google/code-prettify), and wait to see if it's better. It's
quite easy to setup, just follow official guide in Github. Here is a short summary:
1. Embed Prettify into template:  
`<script src="https://cdn.rawgit.com/google/code-prettify/master/loader/run_prettify.js"></script>`

2. Format the source code:
{% highlight html %}
<pre class="prettyprint">  
class Voila {  
public:  
  // Voila  
  static const string VOILA = "Voila";  
  // will not interfere with embedded <a href="#voila2">tags</a>.  
}  
</pre>  
{% endhighlight %}

will become

{% highlight cpp %}
class Voila {  
public:  
  // Voila  
  static const string VOILA = "Voila";  
  // will not interfere with embedded <a href="#voila2">tags</a>.  
}{% endhighlight %}