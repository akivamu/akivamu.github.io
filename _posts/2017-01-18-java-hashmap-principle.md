---
layout: post
title: Java HashMap principle
date: '2017-01-18T23:09:00.002+07:00'
tags:
- Java
---

Been learning Java for a while and using HashMap many times, but today I read an article about HashMap's buckets
length and think I should dig deeper to understand it. This article is to summarize about HashMap, and how it works
internally.

## Basic things about HashMap
HashMap is an implementation of Map interface, basically is a map between KEYs and VALUEs.
HashMap mapping relied on hashing mechanism.

## How HashMap store data internally?
HashMap stores data in an internal **array** (aka **table** or **buckets**) of type `Map.Entry`.  
`Entry` contains important properties: `key` and `value`. It also contains next pointer (will be discussed later)
![Data in HashMap]({{ site.url }}/images/hashmap-data-store.png)
Above image describes data array with n elements. HashMap will increase capacity of this array when needed
(discussed later).

## What happens when adding an entry to HashMap?
This is what we do when add new entry to HashMap:
{% highlight java %}
// Suppose we have a HashMap
HashMap<String, String> infos;

// We want to put this entry to HashMap
String key = "Name";
String value = "John";

// So we have to call:
infos.put(key, value);
{% endhighlight %}
Here are what HashMap will do:
### Step 1\. Calculate hash code
HashMap will do hashing the key into an integer using `key.hashCode()`.  
If key is null, its hash code will be `0`.  
Note that there are cases that different keys can have the same hash code (collision) that we will discuss below.
### Step 2\. Find where to store data in `Entry` table
Using above hash code, HashMap can calculate the position (index) in `Entry` table that can add value to.
Here is the formula (as [explained in SOF](http://stackoverflow.com/a/10879475/6445037){:rel="nofollow" target="_blank"}):
{% highlight java %}
int index = hashcode & table.length;
{% endhighlight %}
### Step 3\. Add `Entry` into table
We can now build an `Entry`, with key and value. Then we set it to the index that calculated above:
{% highlight java %}
table[index] = entry;
{% endhighlight %}
Easy, huh?

#### Solving hash code collision
Now, remember the collision: when different keys have the same hash code. In this case, we have the same index too.
How to store 2 or more entries into same table position?  
We solve it by making a `LinkedList` of `Entry`! And set the head into `Entry` table, at the index we calculated.  
Digging into `Entry` class source code, it contains properties: hashcode, key, value, and a pointer to another
`Entry` - this is where we store the next `Entry`.  
And if there is 3rd Entry, we will store into the pointer in the 2nd `Entry`. And so on.

## What happens when getting from HashMap?
We now get the value by calling:
{% highlight java %}
String name = infos.get("Name");
{% endhighlight %}

Here is what HashMap will do:
### Step 1\. Calculate hash code
Same as when we `put()`
### Step 2\. Find the index of stored entry
Also using the same formula as when we `put()`, we can get the index that key supposed to store to.
### Step 3\. Getting the entry
Just getting data from array table:
{% highlight java %}
Entry entry = table[index];
{% endhighlight %}
In case entry is null, then we just return null as normal.

And now the fun part: remember in case of hash code collision, we put many values into HashMap which has the same hash
code (so the index)? Then how to get correct entry in that LinkedList?

#### `hashCode()` and `equals()`
We keep looping through that list, for each entry, we compare that entry's key with our key, using `equals()` function.
If matched, we return the value, otherwise keep going until end of the linked list.  

Now we can see the different in usage of `hashCode()` and `equals()`:
* Key object’s `hashCode()` is required to calculate the index location of Entry object.
* Key object’s `equals()` method is used to maintain uniqueness of Keys in map.

## The capacity of HashMap
The **capacity** is the size of table (or length).  
The **load factor** is a measure of how full the table is allowed to get before its capacity is automatically increased.  
When HashMap's size (number of entries put into table) exceeds a **threshold** (is equals to **capacity x load factor**),
internal data structures are rebuilt so that the table length has approximately doubled.