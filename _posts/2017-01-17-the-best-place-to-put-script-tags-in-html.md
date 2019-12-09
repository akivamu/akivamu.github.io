---
layout: post
title: The best place to put script tags in HTML
date: '2017-01-17T20:53:00.000+07:00'
tags:
- Frontend
- HTML
---

When browsing a web page, browser will fetch HTML page. It then parses sequentially line by line, and shows HTML
components to user (like title, head...). When encounters a `<script>` tag, browser has to request that script from
URL, and during that time, nothing will be shown to user, not until browser finished requesting that script. It
creates a blocking operation, which is bad in UX.

So we need to solve it, trying to not blocking HTML parser with `<script>` tag.

- Legacy solution: Yahoo! Exceptional Performance team recommends placing scripts **at the bottom** of your page. By
that, all HTML components can be parsed and displayed to user before script started to load.
- Modern approach: modern browsers support the **async** and **defer** attributes on scripts. These attributes tell
the browser it's safe to continue parsing while the scripts are being downloaded. See full answer
[here](http://stackoverflow.com/a/24070373/6445037){:rel="nofollow" target="_blank"}.
- Most correct answer: The best place for it is **just before you need** it and **no sooner**.
