---
layout: post
title: HTTP Multipart
tags:
- Network
---

There are many HTTP `Content-Type`, see [common](https://stackoverflow.com/a/48704300/6445037) [list](http://www.iana.org/assignments/media-types/media-types.xhtml)

For `multipart` type, there are also many [subtypes](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart).

`multipart/form-data` is the most popular:

- [What does enctype='multipart/form-data' mean?](https://stackoverflow.com/questions/4526273)
- [How does HTTP file upload work?](https://stackoverflow.com/questions/8659808)
- [Use Firebug to look more detail in HTTP upload request](http://www.cubicrace.com/2016/05/upload-files-https-using-java.html)

`multipart/x-mixed-replace` is for [server push and streaming](https://en.wikipedia.org/wiki/MIME#Mixed-Replace).
