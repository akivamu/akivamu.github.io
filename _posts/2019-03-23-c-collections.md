---
layout: post
title: C# Collections
tags:
- DotNet
---

![Hierarchy]({{ site.url }}/images/c-generic-collections.jpg)

## The code

There are [some namespaces for collections](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/collections#BKMK_KindsOfCollections):

- System.Collections (e.g. `IList`)
- System.Collections.Generic (e.g. `IList<T>`)
- System.Collections.Concurrent
- ...

Basically, all collections implement `IEnumerable`.

## IEnumerable and IEnumerator

```cs
public interface IEnumerable
{
    IEnumerator GetEnumerator();
}
```

```cs
public interface IEnumerator
{
    object Current { get; }
    bool MoveNext();
    void Reset();
}
```

In short:

- `Enumerable` represents for a dataset contains some items
  - The dataset could be predefined with size/content
  - Or could be undetermined, i.e. the content is lazy loaded
  - [IEnumerable remarks](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=netcore-2.1#remarks)
- `Enumerator` defines the behavior to access to the dataset. E.g.
  - Randomly access item in dataset
  - Orderly access, backward or forward
  - Lazy loading access to new arrived item
  - [IEnumerator remarks](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerator-1?view=netcore-2.1#remarks)
- An `Enumerable` should provide its `Enumerator`
- `Enumerator` can't modify the dataset (readonly)

To access data in an `Enumerable`, we can just call `GetEnumerator()` and perform a while loop to obtains item via methods provided in `Enumerator`.

Or we can use `foreach` for the simplicity:

```cs
IEnumerable<YourType> enumerable = ...;

foreach (YourType item in enumerable)
{
   ...
}
```

We can roughly understand it will be translated to:

```cs
IEnumerator enumerator = enumerable.GetEnumerator();
while (enumerator.MoveNext())
{
   item = enumerator.Current;
   ...
}
```

### Sample implementation

Let's implement our `Enumerable`, which has characteristics:

- Use fixed size array to store data
- Access data in reverse order

```cs
public class MyReverseList : IEnumerable
{
    public readonly object[] Data = new object[10];
    public int Count = 0;

    public void Add(object item)
    {
        Data[Count] = item;
        Count++;
    }

    public IEnumerator GetEnumerator()
    {
        return new MyReverseEnumerator(this);
    }
}

public class MyReverseEnumerator : IEnumerator
{
    private readonly MyReverseList enumerable;
    private int index;

    public MyReverseEnumerator(MyReverseList enumerable)
    {
        this.enumerable = enumerable;
        Reset();
    }

    public object Current => enumerable.Data[index];

    public bool MoveNext()
    {
        if (index > 0)
        {
            index--;
            return true;
        }
        return false;
    }

    public void Reset()
    {
        index = enumerable.Count;
    }
}
```

Usage:

```cs
var myList = new MyReverseList();
myList.Add(1);
myList.Add(2);
myList.Add(3);

foreach (var item in myList)
{
    Console.WriteLine(item);
}
```

We can see the `MyReverseList` looks normal, with an array to store data, a count variable to track dataset size.

The important point here is in `MyReverseEnumerator`, it defines the behavior when accessing data (we do accessing data iterately by `foreach`)  
Changing the behavior in `Enumerator`, e.g. in forward order, we will see the difference.

### Sample implementation - lazy load

This sample demonstrates the lazy load `Enumerable`:

- Only fetch data when needed, infinitely
- Leave user the right to stop enumerating at anytime (here is after 5 accesses)

```cs
public class MyLazyLoadEnumerable : IEnumerable
{
    public IEnumerator GetEnumerator()
    {
        return new MyLazyLoadEnumerator();
    }
}

public class MyLazyLoadEnumerator : IEnumerator
{
    private object currentData = null;

    public object Current => currentData;

    public bool MoveNext()
    {
        currentData = FetchNextData();
        return true;
    }

    // Simulate lazy loading
    private object FetchNextData()
    {
        Thread.Sleep(1);
        return DateTimeOffset.Now.ToUnixTimeMilliseconds();
    }

    public void Reset()
    {
        currentData = null;
    }
}
```

Usage:

```cs
var myLazyEnumerable = new MyLazyLoadEnumerable();
var count = 0;
foreach (var item in myLazyEnumerable)
{
    Console.WriteLine(item);
    if (++count >= 5) break;
}
```

This enumerable is infinite loading, you can't convert it to list or other derived types.  
This code will run forever because `MyLazyLoadEnumerator` always has new data (see method `MoveNext()` return true):

```cs
var list = myLazyEnumerable.Cast<object>().ToList();
```

## ICollection

The [`ICollection`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.icollection-1?view=netcore-2.1#remarks) interface extends `IEnumerable`, and is the base interface for other collections, e.g. `IList`, `IDictionary`...

It adds some methods to manipulate data: `Add`, `Remove`, `Clear`...
