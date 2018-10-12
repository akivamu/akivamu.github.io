---
layout: post
title: First .NET Core application
tags:
- Java
- DotNet
---

# Create app

This post is similar to [Getting started with .NET Core on Windows/Linux/macOS using the command line](https://docs.microsoft.com/en-us/dotnet/core/tutorials/using-with-xplat-cli).

We will go step by step to create simple console app that run on .NET Core.

Install .NET core first, and then we go.

## Prepare working dir
```
mkdir HelloDotnetCore
cd HelloDotnetCore
```

## Initialize app from template
```
dotnet new console
```
Here we use `dotnet` tool to create a `console` application using template. It's similar to
Maven archetype, or Vue-CLI...

The command line output:
```
The template "Console Application" was created successfully.

Processing post-creation actions...
Running 'dotnet restore' on HelloDotnetCore.csproj...
  Restoring packages for HelloDotnetCore.csproj...
  Generating MSBuild file .\obj\HelloDotnetCore.csproj.nuget.g.props.
  Generating MSBuild file .\obj\HelloDotnetCore.csproj.nuget.g.targets.
  Restore completed in 174.98 ms for HelloDotnetCore.csproj.

Restore succeeded.
```

Notice the line:
```
Running 'dotnet restore' on HelloDotnetCore.csproj...
```
From [official docs](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-restore?tabs=netcore2x)
> The dotnet restore command uses NuGet to restore dependencies as well as project-specific tools that are specified in the project file

Yeah, dependency management, good! Just like Maven.  

From .NET Core 2.0, the command `dotnet new` calls `dotnet restore` implicitly.  

> `dotnet restore` calls into NuGet (.NET package manager) to restore the tree of dependencies.
NuGet analyzes the `.csproj` file, downloads the dependencies defined in the file (or grabs
them from a cache on your machine), and writes the `obj/project.assets.json` file, which is
necessary to compile and run the sample.

# Project structure

Here is our folder content:
```
Program.cs
HelloDotnetCore.csproj
obj\
-- HelloDotnetCore.csproj.nuget.g.props
-- HelloDotnetCore.csproj.nuget.g.targets
-- HelloDotnetCore.csproj.nuget.cache
-- project.assets.json
```

First, `Program.cs` is our program source code.

`obj` folder is created when executing `dotnet restore`. We should ignore this folder in CVS.

Project file `HelloDotnetCore.csproj`

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

</Project>
```
See [Understanding the Project File](https://docs.microsoft.com/en-us/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/understanding-the-project-file).  
In short, project file is used for build system, similar to maven `pom.xml`.

In MS world, there are `MSBuild` and `dotnet` CLI tool. See [their relationship](https://stackoverflow.com/questions/43422488/relationship-between-the-dotnet-cli-and-the-new-vs2017-msbuild).  
`dotnet` CLI tool uses MSBuild as its build engine.

# Build the app

We build the app by the command:
```
dotnet build
```
Notice this command also executes `dotnet restore`, result:
```
Microsoft (R) Build Engine version 15.8.166+gd4e8d81a88 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 40.1 ms for HelloDotnetCore.csproj.
  HelloDotnetCore -> .\bin\Debug\netcoreapp2.1\HelloDotnetCore.dll

Build succeeded.
```

The `bin` folder is created:
```
Debug
└───netcoreapp2.1
        HelloDotnetCore.deps.json
        HelloDotnetCore.dll
        HelloDotnetCore.pdb
        HelloDotnetCore.runtimeconfig.dev.json
        HelloDotnetCore.runtimeconfig.json
```
This is debug build. We need these 2 files to be able to run:

- `HelloDotnetCore.dll`
- `HelloDotnetCore.runtimeconfig.json`

Wonder why `.dll` here?. See [this issue](https://github.com/dotnet/cli/issues/6237).

# Run the app

```
dotnet .\bin\Debug\netcoreapp2.1\HelloDotnetCore.dll
```

You can actually build and run in one command:
```
dotnet run
```
