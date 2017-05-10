---
layout: post
title: 'HTTP methods: GET, POST, PUT, PATCH, DELETE'
date: '2017-02-13T23:01:00.000+07:00'
tags:
- REST
---

## POST: create a new resource
{% highlight shell %}
POST /users
{
    "name": "Alice",
    "age": 20
}
{% endhighlight %}

## GET: retrieve a resource
{% highlight shell %}
GET /users/0
# Result is:
{
    "name": "Alice",
    "age": 20
}
{% endhighlight %}

## DELETE: delete a resource
{% highlight shell %}
DELETE /users/0
{% endhighlight %}

## PUT: replace or create a entire resource
{% highlight shell %}
PUT /users/0
{
    "name": "Alice",
    "age": 25
}
{% endhighlight %}
If resource `user` with `id` = `0` not exist, this `PUT` command will behave like `POST` command.

Because this is replacing, we must specify all attributes in PUT body. Missing attributes will be lost, like this
request
{% highlight shell %}
PUT /users/0
{
    "age": 25
}

GET /users/0
# Result is:
{
    "age": 20
}
{% endhighlight %}
Attribute "name" is lost.

## PATCH: replace some attributes of a resource
We use PATCH to change some attributes. Other attributes will remain the same.
{% highlight shell %}
PATCH /users/0
{
    "age": 25
}

GET /users/0
# Result is:
{ 
    "name": "Alice", 
    "age": 20
}
{% endhighlight %}