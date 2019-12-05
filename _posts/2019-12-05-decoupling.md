---
layout: post
title: Dependency and decoupling
tags:
- Software Design
---

## 1. Simplest dependency graph

Let's say we're building an awesome app, but at first, we just have 2 objects (classes): `CoolService` and `SimpleDataStorage`.  

![Simple deps]({{site.url}}/images/dependency-architecture/1_naive.jpg)

`SimpleDataStorage` provides methods to read/write some data from files in local disk.  
`CoolService` uses methods provided by `SimpleDataStorage` to access data, and also does other cool stuffs.  
So, `CoolService` has `SimpleDataStorage` as its dependency, in constructor like this:

```Csharp
class CoolService {
  public CoolService(SimpleDataStorage simpleDataStorage) {
    this.dataStorage = simpleDataStorage;
  }
}
```

We have simple setup like this:

```Csharp
SimpleDataStorage dataStorage = new SimpleDataStorage();
CoolService service = new CoolService(dataStorage);
```

Things work well for now.

## 2. Multiple implementations

One day, we see that accessing data from local file is too slow, we want to put data on memory.

We can modify the code in `SimpleDataStorage` to handle in-memory data.  
Or more careful, create new class `MemDataStorage`, and keep the `SimpleDataStorage` in case we want to
revert back to use local file storage.

But, we still need to change `CoolService`:

```Csharp
class CoolService {
  public CoolService(MemDataStorage memDataStorage) {
    this.dataStorage = memDataStorage;
  }
}
```

We will try to keep `MemDataStorage` to has same method signature as `SimpleDataStorage` to minimize code
change inside `CoolService`. But at the very least, we still have to change the constructor.

Here `CoolService` **depends on the concrete implementation** (`MemDataStorage` or `SimpleDataStorage`).  
And we violate the **D** in **[SOLID](https://en.wikipedia.org/wiki/SOLID)**. You can see the inconvenience
where replacing multiple implementations.

## 3. Depend upon abstractions, not concretions

![Abstraction]({{site.url}}/images/dependency-architecture/2_abstraction.jpg)

Here we introduce new interface `DataStorage`, defines generic behavior for data access.  
We also have 2 implementations of that interface: `FileDataStorage` and `MemDataStorage`.  
These classes implement the behavior of interface in specific way (File vs Mem)

Now, the `CoolService` depends on the interface (abstraction), not concrete implementation:

```Csharp
class CoolService {
  public CoolService(DataStorage dataStorage) {
    this.dataStorage = dataStorage;
  }
}
```

And the setup:

```Csharp
DataStorage dataStorage = new FileDataStorage();
// Or
DataStorage dataStorage = new MemDataStorage();

CoolService service = new CoolService(dataStorage);
```

Whenever we want to change implementation, just swap it out in the setup code, without touching the code
inside `CoolService`.

Great! Now we achieve the loose coupling dependence.

## 4. Even looser: packages

Let's say, these components `CoolService`, `FileDataStorage` and `MemDataStorage` become bigger and bigger,
with a lot of complex logic.

We decide to split into multiple teams, 1 for each component. Dont want to share same code repo, so we also split the system into packages. This is the draft design:

![Split team]({{site.url}}/images/dependency-architecture/3_packages.jpg)

Here we split into Service and DataAccess package. But this is not good enough:

- At the package level, Service still depends on implementation (`FileDataStorage` and `MemDataStorage`)
- `FileDataStorage` and `MemDataStorage` still share same package

So, split even more:

![Split more]({{site.url}}/images/dependency-architecture/3_packages_2.jpg)

Here we achieve our best decoupling:

- Each team can work on each component (package) independently
- All teams agree (and depend on) the contract: the package CommonInterface
- The contract is defined by highest level design, all relevant component rely on it

## 5. Wiring things up

Until now, all are just at the design time.

You may ask how `CoolService` can call the implementations
(`FileDataStorage` and `MemDataStorage`) at runtime if it doesn't depend on them?

Here comes: the **host app**, a separate component which has access to all components. For each interface
(like `DataStorage`), it will choose the concrete implementation (`FileDataStorage` or `MemDataStorage`)
and construct it. Then use it to construct the `CoolService`, and so on.

When the dependency tree is ready, it then run the app, handle requests...etc...
