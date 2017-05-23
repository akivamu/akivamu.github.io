---
layout: post
title: Data storage in Android filesystem
tags:
- Android
---

In Android OS, application can have many options to store local data (offline, in filesystem). We will go through to
understand how application see the data storage and how Android manages it underneath.

## Shared Preferences

### Application aspect
- Used to store primitive data (e.g. int, boolean, String…)
- Key-value pairs. For example:
  - “address” -> “123 Wall Street”
  - “id” -> 132
  - “isPaid” -> true
- Can’t be accessed by other apps.
- Will be removed when user uninstall app.

### Filesystem aspect
- All data is stored in XML files, located at: `/data/data/<app.package.name>/shared_prefs/`
- Can be accessed by user with root privilege.

## Internal Storage
Here, the word `Internal Storage` means the folder where app is installed. Not to be confused with Internal SD Card.

### Application aspect
- Used to store normal files/folders
- Can’t be accessed by other apps.
- Will be removed when user uninstall app.
- Android provided some APIs, basically it's just like working with normal file.

`Context.getCacheDir()` return object `File` point to folder `/data/data/<app.package.name>/caches/`  
`Context.getFilesDir()` return object `File` point to folder `/data/data/<app.package.name>/files/`  
`Context.getDir(<dir_name>,<mode>)` create and return object `File` point to folder `/data/data/<app.package.name>/app_<dir_name>/`

### Filesystem aspect
Data is stored as normal file, at folder `/data/data/<app.package.name>/files/`

## External Storage
There are 2 types:
- Removable storage: like external SD Card (you can physical remove it).
- Non-removable: on-board flash memory (NAND/NOR), partitioned to be used as external storage.

One device can have multi external storage (1 is called primary). From now on, assume that we use primary, and
located at `/sdcard`.

Also pay attention to these permissions:
- android.permission.WRITE_EXTERNAL_STORAGE
- android.permission.READ_EXTERNAL_STORAGE

### Application aspect
- Used to store normal files/folders.
- Can be accessed by other apps.

### Filesystem aspect
- Public storage (system-wide access, won’t be deleted when uninstalling):
  - `Environment.getExternalStorageDirectory()` return File point to the top-level primary external storage directory.
  i.e. `/sdcard`. May leave garbage to user, should not use.
  - `Environment.getExternalStoragePublicDirectory(<type>)` return File point to some common public folder.
  i.e. `/sdcard/Music`, `/sdcard/DCIM`
- Private storage API (app access, will be deleted when uninstalling):
  - `Context.getExternalFilesDir(null)` to get top-level dir. i.e. `/sdcard/Android/<app.package.name>/files`
  - `Context.getExternalFilesDir(<type>)` return File point to some common app-private folder. i.e.
  `/sdcard/Android/<app.package.name>/files/Pictures`

## Database