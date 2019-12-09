---
layout: post
title: Dependency Inversion
tags:
- Software Design
---

## Concepts

Taking from [Why Inverse Dependency](https://stackoverflow.com/a/1113937/6445037):

- Reversing the direction of interface ownership: "lower level" components are dependent upon the interfaces
**owned by** the "higher level" components
- The ownership of interface is **shifted to more valuable** components (higher level)
- The "high level" components define the interface (`INeedSomething`), "low level" components implement it
  - Contrast to traditional: "low level" components define the interface (`IDoSomething`)

Shouldn't be confused with [abstraction and decoupling]({{site.url}}/decoupling).

## Example

### Traditional dependency direction

![Tradition]({{site.url}}/images/dependency-architecture/dip-tradition.jpg)

Here we see the `DataStorage` interface may define some methods like:

```Cs
interface DataStorage {
  byte[] GetBytes(string dataName);
  DataStream GetStream(string dataName);
}
```

Basically, the "low level" component (DataAccess) defines the interface, data structure (`IDoSomething`)  
The "high level" component (Service) uses (and depends) on this defined contract.

### Inversed dependency direction

![Tradition]({{site.url}}/images/dependency-architecture/dip-inversed.jpg)

Here we see the "high level" component (Service) defines the interface `ServiceDataStorage`:

```Cs
interface ServiceDataStorage {
  Image GetImage(string dataName);
  Text GetText(string dataName);
}
```

The "low level" component (DataAccess) implements the detail, follow the contract provided by high level.

This actually means: the Service **need something**, the DataAccess has to fulfil that need.

Hence, reversing the dependency.

## Comparison

Key notes:

- The value and role of component:
  - Tradition: Low level component is more important, it defines the behavior which is barely changed. It's in the center that other app use it.
  - Inversed: The high level component is more important, it defines the behavior. Others need to provide stuffs around it.
  - Who owns the contract is the center, most important.
- Reusability:
  - Tradition: the low level component can be reused in other app (other services) since it provide primitive operation. E.g. when you build a util library.
  - Inversed: the high level component can be reused in other app (difference data access mechanism). E.g. when you build a business app that has core logic, but with replacable detail (Web UI, Console UI, SQL db, noSQL db...)

## Uncle Bob's Clean Architecture

Clean Architecture promoted by Uncle Bob is an example of reversing the dependency:
[Detail explanation](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

## Bonus: vs Adapter pattern

[Adapter pattern](https://en.wikipedia.org/wiki/Adapter_pattern) looks similar to reverse dependency.

![Adapter pattern](https://upload.wikimedia.org/wikipedia/commons/e/e5/W3sDesign_Adapter_Design_Pattern_UML.jpg)

Here we can see the interface `Target` is defined, and the detail `Adapter` implements it utilize some detail
provided by `Adaptee`

Looks similar, but again, the direction of dependency is based on **who owns the contract**.

If `Client` (high level) defined the `Target` interface, then it's reversed dependency.  
If `Adapter` (low level) defined the `Target` interface, then it's just tradition dependency, since the behavior is defined by the low level, high level component has to rely on it.

## Extras

- [examining-dependency-inversion](http://aspiringcraftsman.com/2008/12/28/examining-dependency-inversion/)
- [art-of-separation-of-concerns](http://aspiringcraftsman.com/2008/01/03/art-of-separation-of-concerns/)
