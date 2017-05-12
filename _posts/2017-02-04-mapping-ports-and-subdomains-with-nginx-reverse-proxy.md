---
layout: post
title: Mapping ports and sub-domains with NGINX reverse proxy
tags:
- DevOps
---

This is simple step by step guide to setup a Reverse Proxy using nginx in Linux. Installing step takes Ubuntu for example, but you can go to
[nginx page](http://nginx.org/en/download.html){:rel="nofollow" target="_blank"} for other platform install guide.

## Intro
For simplicity, let assume our situation like this:  
* We have a domain **example.com**.
* We have a server, running Ubuntu for example.
* This server running 2 web pages, in separate HTTP ports: **1111** and **2222**.
* We want to use root domain **example.com** to serve web page in port **1111**.
* We want to add a blog to sub domain **blog.example.com**, which serve web page in port **2222**.

In short, we want to forward visitor like this:  
* example.com       -> localhost:1111
* blog.example.com  -> localhost:2222

## Install
Here is install for Ubuntu with apt tool. For other OS, you can go to nginx home page for detail.
{% highlight shell %}
apt install nginx  
{% endhighlight %}

## Declare sites
We declare rules for proxy by create 2 files in `/etc/nginx/sites-available/`

For root domain **example.com**:
{% highlight shell %}
nano /etc/nginx/sites-available/example.com
{% endhighlight %}

{% highlight shell %}
server {
  listen 80;
  server_name example.com;
  location / {
    proxy_pass http://127.0.0.1:1111;
    include /etc/nginx/proxy_params;
  }
}
{% endhighlight %}

For sub domain **blog.example.com**:
{% highlight shell %}
nano /etc/nginx/sites-available/blog.example.com
{% endhighlight %}

{% highlight shell %}
server {
  listen 80;
  server_name blog.example.com;
  location / {
    proxy_pass http://127.0.0.1:2222;
    include /etc/nginx/proxy_params;
  }
}
{% endhighlight %}

## Enable sites
Link your sites configuration from folder `sites-available` to folder `sites-enabled`:
{% highlight shell %}
ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/example.com
ln -s /etc/nginx/sites-available/blog.example.com /etc/nginx/sites-enabled/blog.example.com
{% endhighlight %}

## Restart nginx
{% highlight shell %}
service nginx restart
{% endhighlight %}