---
layout: post
title: All about access token
tags:
- Security
---

In client-server communication, a client makes request to server, and server responses to that request.

For security reason, server need to verify the identity of the request from client.  
So, the request itself must includes a meta-data, which can be used by server to identify the client.

There are many ways that meta-data can be, e.g. cookie, credential, token...

This post is about token based authentication.  

A simple flow:

- Step 1: Client requests for token  
  - First, client must identifies itself to server (by other credential like username/password or secret...)
  - Server check that identity. If valid then generate a token and response that token to client

- Step 2: Client use token to access resource
  - To request for data, client must attaches the token to the request.  
  - Server verifies that token, and response data.

# Simple token approach

Server must stores list of tokens in database (can be memory-based, cache-based or real database like SQL)  

For each request, server must queries the database for these tasks:

- Check if token exist (already issued to client)
- Check validity if token is in valid format and contains valid information

## Revoke token

For example, if client logout, we don't want to allow that token to access to server data.

So, server will deletes that token from database, or just marks it as invalid.  
Further requests using that token will be rejected by server.

## Drawback: performance issue

This approach has a drawback: each request always **requires a database query** to check token, which may leads to
**performance issue**.

We can solve it by self-contained token approach.

# Self-contained token approach

What if we can verify token WITHOUT querying the database?

Self-contained token means the token itself contains all information which can be used to verify itself by issuer (server).  
Let's inspect a example mechanism: [JSON Web Token](https://jwt.io/).

## JSON Web Token

JWT is a string in this format: xxx.yyy.zzz

- xxx is encoded Header
- yyy is encoded Payload
- zzz is encoded Signature

Please see [JSON Web Token](https://jwt.io/) for detail.

Summary flow:

- Step 1: Server generates token
  - Server (token issuer) has a `secret`
  - Server generates `Header` and `Payload`. The `Payload` contains info about a client
  - Server then digitally signs those info with its `secret` to get `Signature`
  - Server combines `Header`, `Payload` and `Signature`, and result a token
- Step 2: Server provides to client that token. Client then uses that token to request for data
- Step 3: In each request
  - Server uses `secret` to verify the token (mainly verify the `Signature` to know the token is issued by server)
  - There is no need to access the database in verification step
  - Response to client

## Drawback: can't revoke a token

We can see that there is no need to access the database to verify a token.

But it will leads to a security issue: the token is **always valid**, even if client logout. Because the token is
self-contained, there is no way to make it invalid.

Wait, there is.

### Solution 1: maintain a list of invalid tokens (bad idea)

The server keeps in database a list of invalid tokens.  
When client requests logout, server adds that token into invalid token list.

But, then for each request, server need to query database to check if a token is marked as invalid.  
It leads to the **performance issue in the beginning**.

### Solution 2: token expiration

We make the token to be valid in short duration, like 5 minutes, 1 hour...  
That expiration data can be included in `Payload`.

When client logout, that token is still valid, but just for short duration.  
After expired, server fails to verify that token, and then rejects all further requests.

By that way, the validity of token can be verified WITHOUT database querying. Sound good?

Actually not so good!

#### Drawback: security hole in expiration window

After client logout, the token is still valid until expiration time.  
It's expiration time window security hole.

Any solution for that?  
Short answer: NO

But we can reduce that security issue, by making token lifetime very short, like 5 minutes.

#### Drawback: need to relogin frequently

When token expired, client need to login again.  
If token lifetime is short, client need to login frequently. It's very inconvenient, another drawback?

Yeah yeah, okay, use **refresh token**!

# Access token - Refresh token approach

In this approach, we use 2 tokens:

- Access token
  - Attached to request, used to access data
  - Short-lived
- Refresh token
  - Used to get new Access token
  - Long-lived

Simple flow:

- When logging in, server generates those 2 tokens, and send to client.
  - Server stores Refresh token in database
  - Server DO NOT store Access token in database 
- Client uses Access token to access data.
- When Access token expired, the request is rejected. Then client sends Refresh token to server
  - Server queries database to check validity of Refresh token.
  - Generate new Access token, send it back to client.
  - Note that client **no need to login again** here, just request refresh token.
- Client uses new Access token to access data.
- Repeat

When Refresh token expired:

- Client sends Refresh token to server, but is rejected because Refresh token is expired.
- Client login again (with username/password)
- Server generates new 2 tokens and send to client
- Work as normal

When client logout:

- Server deletes Refresh token from database
- Further refreshing token requests are rejected

This approach solves problems:

- Client no need to relogin too frequently. Instead, just request for refreshing new token.
- Server only hits database when refreshing new token. Compared to the case hits database on every request.
- Access token life time can be reduced to minimize security window.

# Choosing the right approach

## 1. High security system

Basically, you want to deny access immediately after logged out, and a token can be used once.

Suggestion:

- Use very short-lived token (a couple of minutes), no need to be self-contained. No need to separate refresh token.
- Server stores token in database (mem, cache, file or dbms)
- Server queries database to verify token in every request.
- Refreshing token in every request with valid token:
  - Server deletes old token in database
  - Generates new token and send back to client in response
  - Client get new token, and use it in next request
- Server deletes token from database when expired or client logged out.

About this approach:

- Client don't need to relogin too frequently.
- One-time token: any token is invalidated after each request.
- If a token is leaked, client can logout, and that token is invalid instantly.
- But the server will suffers from high load and database access.

## 2. Low security system

Suggestion:

- Use short-lived Access token (hours...), self-contained.
- Use long-lived Refresh token (days, weeks or permanent).
- Server stores only Refresh token in database.
- Server verify Access token in every request, no database access.
- If near expire time, client request for refreshing new Access token.
  - Server queries database for Refresh token, and generate new Access token, send to client
- Server deletes Refresh token from database when client logged out.

By that approach:

- Client don't need to relogin too frequently. If Refresh token is permanent, there is no need to relogin at all.
- Low load in database access (every time Access token expired)
- It's important that client must keep Refresh token in secret
- Expiration window security hole

Trade off in performance vs security:

- Increase security: the shorter Access token lives (so does the security hole window)
- Decrease performance: the more database accesses needed.

## 3. Middle land approach: no security hole window

We modify the 2nd approach a bit:

- Use short-lived Access token (hours...), self-contained.
- Use long-lived Refresh token (days, weeks or permanent).
  - Server stores Refresh token in database.
- In addition, server stores a list of **revoked Access tokens** in database.
- For each request:
  - Server verify Access token by `secret`, no need database access
  - If Access token is valid, server further queries database to check if token is revoked
  - Server only accepts request if it's valid and NOT in revoked list
- For Refresh token, server access to database to verify
- When client logout:
  - Server deletes Refresh token in database
  - Server adds Access token to revoked list in database

Here we eliminate security hole window when Access token not expired yet, by sacrifice some performance: the addition
step to query database to check if Access token is revoked or not.

Because the revoked list is small (compared to when we store whole Access token list), the query should perform with
better performance.

We can improve performance by using fast database (in-memory and cache) to store revoked Access token list.

# Summary

Here are the benefits of these features:

- Self-contained token: no need database hit when verifying.
- Token expiration: increase security in case self-contained token is leaked.
- Refresh token: no need to re-login.