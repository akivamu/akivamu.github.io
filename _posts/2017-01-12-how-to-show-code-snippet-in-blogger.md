---
layout: post
title: How to show code snippet in Blogger
tags:
- Frontend
- Blogger
---

Blogs that related to IT and Software Engineering will sometime need to show source code to reader. But not just
displayed as plain text, it's better if source code can be formatted with color, indentation, alignment...just like
be displayed in IDE.  
In web front-end, it can be implemented by using Javascript and CSS. Checking around some open source libraries/utils,
I found that [SyntaxHighlighter](https://github.com/syntaxhighlighter/syntaxhighlighter) is well known one. Below is
step-by-step guide to use this library in Blogger.

### Step 1: Introduction
SyntaxHighlighter is open source, and it consists of many modules: core, brush, themes... There are 2 ways to get this
library:
* Method 1: Link pre-built version of SyntaxHighlighter into Blogger.
* Method 2: Download SyntaxHighlighter source code and build it.

For method #1, we get pre-built version from this [CDN](https://cdnjs.com/libraries/SyntaxHighlighter), and embed into
Blogger template, just follow below guide.  
For method #2, follow this [official guide](https://github.com/syntaxhighlighter/syntaxhighlighter/wiki/Building) then
upload your built binary to your host and embed it to Blogger. Quite similar to method #1, just change URL.

### Step 2: Setup SyntaxHighlighter into Blogger template
Open Blogger HTML template, add these lines into `<head> tag:
{% highlight html %}
<link href='https://cdnjs.cloudflare.com/ajax/libs/SyntaxHighlighter/3.0.83/styles/shCore.min.css' rel='stylesheet' type='text/css'/>  
<link href='https://cdnjs.cloudflare.com/ajax/libs/SyntaxHighlighter/3.0.83/styles/shThemeDefault.min.css' rel='stylesheet' type='text/css'/>  
<script src='https://cdnjs.cloudflare.com/ajax/libs/SyntaxHighlighter/3.0.83/scripts/shCore.min.js' type='text/javascript'></script>  
<script src='https://cdnjs.cloudflare.com/ajax/libs/SyntaxHighlighter/3.0.83/scripts/shBrushXml.min.js' type='text/javascript'></script>  
<script language='javascript'>  
  SyntaxHighlighter.config.bloggerMode = true;  
  SyntaxHighlighter.all();  
</script>  
{% endhighlight %}
See the file `shBrushXml.min.js`? It is decorator for XML language. If you want to display source code in other
programming language, just go to CDN find URL and add more type similar to this one.  
You can also choose the color theme, [SyntaxHighlighter supports many themes](https://github.com/syntaxhighlighter/syntaxhighlighter/wiki/Brushes-and-Themes).  

### Step 3: Format source code in blog post
There are 2 methods, you can refer to [official guide](https://github.com/syntaxhighlighter/syntaxhighlighter/wiki/Usage)
for pros/cons of each method and choose the best for you. Here I just demonstrate by simple examples:

#### Method 1: Using `<pre>` tag

{% highlight html %}
<pre class="brush: js">
function foo()  
{  
    console.log('Hello world');  
}  
</pre>
{% endhighlight %}

Will become  

{% highlight js %}
function foo()  
{  
    console.log('Hello world');  
}  
{% endhighlight %}

#### Method 2: Using `<script>` tag

{% highlight html %}
<script type="text/syntaxhighlighter" class="brush: js"><![CDATA[  
function foo()  
{  
    console.log('Hello world');  
}  
]]></script>  
{% endhighlight %}

Will become

{% highlight js %}
function foo()  
{  
    console.log('Hello world');  
}  
{% endhighlight %}