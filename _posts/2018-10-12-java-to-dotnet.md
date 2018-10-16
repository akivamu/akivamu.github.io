---
layout: post
title: From Java world to .NET world
tags:
- Java
- DotNet
---

# Overview

.NET is similar to JRE.

## Fragmentation in .NET architecture

![]({{ site.url }}/images/dotnet.png)

.NET also suffers from fragmentation problem. In above figure, there are 3 implementations:

- .NET Framework: the oldest, support Desktop and Webapp. Run on Windows only.
- .NET Core: Run on cross platform, specially for Webapp. Open-source.
- Xamarin: Run on mobile. Open-source.

In those 3 implementations, there are some duplicated parts, like Base Libraries.
So .NET Standard is introduced. And it's open-source.

## .NET Standard

![]({{ site.url }}/images/dotnet-standard.png)

[Demystifying .NET Core and .NET Standard](https://msdn.microsoft.com/en-us/magazine/mt842506.aspx)

## Visual Studio

In other worlds (Java, JS, Python...), there are so many options for development:

- IDE: Eclipse, IntelliJ, Notepad...
- Build: Maven, Gradle, npm...

In .NET world, the development tool is simple:

- IDE: just use Visual Studio
- Build: MSBuild, NuGet

You just install and use VS. It has everything you need.  
But Visual Studio has limitations, e.g. Window only, GUI only, too heavy...

And the command line tool is created: **.NET Core CLI**


# Useful documents

- Official docs: [MS .NET Guide](https://docs.microsoft.com/en-us/dotnet/welcome)
- [.NET Core get started tutorials](https://docs.microsoft.com/en-us/dotnet/core/get-started)
