---
layout: post
title: Blogger template structure explained
tags:
- Frontend
- Blogger
---

Blogger allowed to customize the appearance of our blog, by editing Blogger template - an HTML makeup code.  
The structure of Blogger template is quite similar to every HTML pages with standard HTML components (head, body,
div...).

In addition, Blogger introduce these new special syntax:
* Special tag `<data:*>`
* Tags with namespace b, like `<b:section>`, `<b:widget>`...
* Attribute `expr:*`

### In a simple manner
* Standard HTML tags are used for **styling** and **defining layout**.
* Blogger specific tags are used for **collecting dynamic data** (posts, comments...) to display, also
**declaring common components** (widgets) that can be reused around blog.

## Some concepts

### Skin: `<b:skin>` tag

*   Placed inside `<head>` block.
*   Contains all custom CSS code.
*   Support variable.
*   See detail reference document here: [https://support.google.com/blogger/answer/46871](https://support.google.com/blogger/answer/46871).

### Sections: `<b:section>` tag

*   Placed inside `<body>` block.
*   Represent areas like header, footer, sidebar...
*   Contain only widgets.
*   See detail reference document here: [https://support.google.com/blogger/answer/46888](https://support.google.com/blogger/answer/46888).

### Widgets: `<b:widget>` tag

*   Placed inside `section` block
*   Every widget has to be specified a predefined type: ?list here?
*   Contain only `includable`(s).
*   See detail reference document here: [https://support.google.com/blogger/answer/46888](https://support.google.com/blogger/answer/46888).

### Includables: `<b:includable>` tag

*   Placed inside `widget` block
*   Every widget must have at least 1 `includable` with `id` is `main`, and it will be displayed.
*   If widget contains more than 1 `includable` with `id=main`, others won't be displayed. But they can be included
into `main` includable to be displayed.
*   See detail reference document here: [https://support.google.com/blogger/answer/46995](https://support.google.com/blogger/answer/46995).

### `<b:include>` tag

*   Technically can be placed anywhere in page
*   Is used to include a `includable`
*   See detail reference document here: [https://support.google.com/blogger/answer/46995](https://support.google.com/blogger/answer/46995).

### `<data:*>` tag

*   Is used to get dynamic data
*   E.g. `<data:title>` will represent for the blog's title.
*   See detail reference document here: [https://support.google.com/blogger/answer/47270](https://support.google.com/blogger/answer/47270).

### `expr` attribute

*   Is used to get dynamic data and pass into other tag attribute
*   E.g. `<a expr:href='data:blog.homepageUrl'>Home</a>` will set page url to attribute `href`

### Other tags

See detail reference document here: [https://support.google.com/blogger/answer/46995](https://support.google.com/blogger/answer/46995).  

## Simple template

![Simple blogger template]({{ site.url }}/images/sample-blogger-template.png)

## References
http://thoughtsomething.blogspot.com/2009/01/understanding-blogger-template-1.html  
http://www.oneminuteinfo.com/2011/07/html-template-syntax-in-google-blogger.html  
https://support.google.com/blogger/answer/46995