---
layout: post
title: OAuth and OpenID Connect
tags:
- Software Design
- Security
---

Authentication (and Authorization) is an important part of our internet for a long time.
This post will walkthrough how this problem is solved, from the simplest form into modern approach.

## Beginning

We start with a monolithic app that does everything:
- The app business logic as always
- Auth suport (login, logout, permissions...)
- User/identity management

![Single app]({{site.url}}/images/auth/single-app.jpg)

Then we expand our business, create more apps. Quick approach is like this:

![Multiple apps]({{site.url}}/images/auth/multi-apps.jpg)

We can see problems here: duplicated auth part on every single app.

Now, we split authentication part into a separate server.

![Multiple apps - Single Auth Server]({{site.url}}/images/auth/multi-apps-single-as.jpg)

All apps will use this auth server for login, logout, user management..., they don't have to implement those features.

## OAuth2

OAuth (Open Authorization) is a standard for **access delegation**.

It's always easier to understand with an example.

Let's say you have an Google account, you will have access to GMail, GDrive... we can have these concepts:
- Resource: everything that attached to you in Google world e.g. your email, telephone or the right to access Google services like Gmail, Gdrive...
- Resource owner: it's you.
- Authorization server: in simplest form, it's the server provided by Google, where you can login/logout.
This server knows about you and your resources.

Next, suppose you want to use a 3rd party app to manage the files in your Google Drive.  
This app needs to read/write to your GDrive storage, you need to give it permission. How?  
You can't give it your google account username/password, because apparently not secure: user/pass to your full account is exposed.

Here comes OAuth "access delegation": you delegate the access to your GDrive to the 3rd party app. We have another concept:
- Client: the one who want to access to the resource i.e. here is the 3rd party app.

Detail flow is everywhere:
- https://oauth.net/2/
- https://aaronparecki.com/oauth-2-simplified/
- https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2

### OAuth for **pseudo-authentication**

OAuth is NOT for **authentication** purpose.  
But we can use it as **pseudo-authentication** method. See [wikipedia](https://en.wikipedia.org/wiki/OAuth#OpenID_vs._pseudo-authentication_using_OAuth).

Basically, if user is able to delegate access to a client, then client can know he/she is the owner of resource.

### OAuth for Single Sign On

Let's take a very common example: Login with Google, Login with Facebook...

This is actually done by OAuth protocol:
- A 3rd party app requests access to some of your info (e.g. email) that stored in Google.
- You allow it (delegate), and then the app can know who you are.
- You're authenticated.

Here, you delegate the access to your info (which is also a resource), instead of a GDrive file compared to the example above.

Those authentication things are not strongly defined by OAuth (and it's not intended to).

Hence comes the OpenID Connect.

## OpenID Connect

The **authentication** protocol bases on top of OAuth2.

It simply adds more explicit definitions to the underlying OAuth2:
- Builtin scopes: openid, profile, email, address, phone
- Tokens: id_token, access_token, refresh_token
- New concepts:
  - Identity: the information about user
  - Identity Provider: if the Authorization server (defined above in OAuth) supports OIDC, it's called Identity Provider. It provides identity information to the client.

The authentication flow is similar to OAuth2. See [OIDC Primer](https://developer.okta.com/blog/2017/07/25/oidc-primer-part-2).

See more: [OAuth vs OpenID](https://stackoverflow.com/a/6915454/6445037)

## Extra ref: [Attacking the OAuth Protocol](https://dhavalkapil.com/blogs/Attacking-the-OAuth-Protocol/)

The article points out some "weaknesses" of the protocol. Correct, but those points are described clearly in the spec (with recommended mitigation and best practice for implementation)

Read the article for detail attacks, here are my comments

### 1 and 3. CSRF during authz

Here 2 conditions are needed
- Attacker is able to attack CSRF to victim (going to modified auth url)
- The client failed (or didn't) to verify the redirect from OAuth server.

The 2nd condition is the fault of client. Client should verify the redirect is correct. One tool is using the `state` parameter:
- When initiating the OAuth flow, client will pass an unpredictable string into `state` parameter.
- When OAuth server redirects to client, the `state` is included, so that client can verify it.

### 2. Attacking `redirect_uri`

Here are the conditions:
- Attacker is able to attack CSRF to victim (going to modified auth url)
- On the client, there is a page with XSS vulnerable.
- The page with XSS must be registered as whitelisted redirect_uri.

Again 2nd point is the fault of client. Furthermore, if there is XSS there, attacker can have much more dangerous attack, without attacking OAuth itself.

For 3rd point, whitelist redirect_uri is very basic case and already defined in the spec. Major implementations of OAuth server are definitely don't have this bug.

### 4. Reusing an access token - One access_token to rule them all

Not sure how two clients can accept the same token. If client 2 accept any token, that's shitty client.

### 5. Open Redirect in OAuth 2.0

Again this is the implementation detail of the OAuth server, that doesn't whitelist the redirect_uri and doesn't validate the request params first.


## Extra: fine-grained authz in ASP.NET Core

https://www.thereformedprogrammer.net/a-better-way-to-handle-authorization-in-asp-net-core/
