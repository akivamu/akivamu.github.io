---
layout: post
title: Package management
tags:
- Linux
---

## Concepts

- Package: an archive of software
  - Contains resource, docs, config and pre-compiled binary of the software itself
  - May not contains the dependencies or libraries.
  - But instead specifiy the information about dependencies
- Dependency: also a package, act as library for other packages to reference to
  - May also specify other dependencies
- Repository: a webhost to store packages
- Package manager: a software installed in a Linux distribution
  - Helps finding packages in software store/repository
  - Manage installation, update, remove...
  - Auto find/resolve/update dependencies for a package

Reference: [Linode - Linux package management](https://www.linode.com/docs/tools-reference/linux-package-management/)

## Common package management systems in Linux

There are many package manager for linux systems

### Debian/Ubuntu

This is Debian-based distributions like Ubuntu, Mint, Raspian...

At lowest level, system uses `.deb` package format.  
The cmd line tool to install a package file is `dpkg`.  

Advanced Packaging Tool (`apt`) is package manager, uses `dpkg` under the hood.  
It supports finding/installing packages in software repositories.  
Software repositories are managed in file `/etc/apt/sources.list`.  

### Other systems

Ref: [apt-yum-dnf-pkg](https://www.digitalocean.com/community/tutorials/package-management-basics-apt-yum-dnf-pkg)

## Package management in Windows

Some populars in Windows systems: Scoop, Chocolatey...

Ref: [Setup dev env for Windows](https://medium.com/@pranjalpaliwal/perfect-dev-environment-on-windows-the-easiest-way-691c649850eb)
