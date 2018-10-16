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

# Dependencies Management

[NuGet](https://www.nuget.org/) is dependencies manager for .NET. It's similar to maven in
Java world, or npm in JavaScript.

Let's add a dependency to our project: [Newtonsoft.Json](https://www.nuget.org/packages/newtonsoft.json/)

```
dotnet add package Newtonsoft.Json --version 11.0.2
```

After executed, we can see these lines are added to our project file `HelloDotnetCore.csproj`:
```xml
<ItemGroup>
  <PackageReference Include="Newtonsoft.Json" Version="11.0.2" />
</ItemGroup>
```

Feel free to play with it in our code `Program.cs`.

# Build and Run

We build the app by the command [dotnet build](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-build):
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

This command uses `MSBuild` under the hood.

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

- `HelloDotnetCore.dll`
  - Binary files in Intermediate Language (IL)
  - Wonder why `.dll` here? See [this issue](https://github.com/dotnet/cli/issues/6237).
- `HelloDotnetCore.pdb`
  - Symbol files for debugging
- `HelloDotnetCore.deps.json`
  - List of the dependencies of the application.
  - You can see `Newtonsoft.Json` library is listed in this file.
- `HelloDotnetCore.runtimeconfig.json` and `HelloDotnetCore.runtimeconfig.dev.json`
  - Specifies the shared runtime and its version for the application

**Note:** this build did NOT include external libraries (like `Newtonsoft.Json` from NuGet).
So we can't bring this output folder to another computer to run.

## Run the app

```
dotnet .\bin\Debug\netcoreapp2.1\HelloDotnetCore.dll
```

You can actually build and run in one command:
```
dotnet run
```

# Build for deployment

There are [2 types of deployment](https://docs.microsoft.com/en-us/dotnet/core/deploying/index):

- Framework-dependent deployments (FDD)
  - The app contains only its own code and any third-party dependencies
  - The system provides run-time environment
- Self-contained deployments (SCD)
  - The app contains all code, libs and runtime

See tutorial [Deploying .NET Core apps with command-line interface (CLI) tools](https://docs.microsoft.com/en-us/dotnet/core/deploying/deploy-with-cli)

## Framework-dependent deployments

```
dotnet publish -f netcoreapp2.1 -c Release
```

Similar to `dotnet build`, but with additional action: copy dependencies from the NuGet
cache into the output folder.

We can see new folder `Release` and sub folder `publish`

```
Release
└─netcoreapp2.1
  └─publish
        HelloDotnetCore.deps.json
        HelloDotnetCore.dll
        HelloDotnetCore.pdb
        HelloDotnetCore.runtimeconfig.json
        Newtonsoft.Json.dll
```

`Newtonsoft.Json.dll` is copied to from NuGet cache to this folder.  
Now we can bring this `publish` folder to another computer (with .NET Core 2.1 installed)
and run.

## Self-contained deployment

Publish with OS platform specified by `-r` params:
```
dotnet publish -r win10-x64 -c Release
```

We can see new folder `win10-x64` in `bin\Release\netcoreapp2.1\`.

The `publish` folder contains a lot of files, included our application and .NET runtime.

Bring this `publish` folder to another computer (without any .NET runtime) and run `.exe` file




