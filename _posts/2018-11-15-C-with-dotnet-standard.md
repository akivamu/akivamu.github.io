---
layout: post
title: Use C++ inside .NET Standard/Core
tags:
- DotNet
---

At the writing time, [C++/CLI isn't supported in .NET Core](https://stackoverflow.com/questions/39140014/c-cli-support-in-net-core).  
So that you can't use C++ code directly inside .NET project (like .NET Framework).  
We have to use [P/Invoke](https://docs.microsoft.com/en-us/dotnet/framework/interop/platform-invoke-examples).

Steps:

- Build C++ into DLL, that exports public entry points to be used outside.
- In .NET, use P/Invoke to load C++ DLL file, map DLL exported entry points into .NET methods.

[See my example project in gitlab](https://gitlab.com/akivamu/ExampleCppWithDotnetStandard)