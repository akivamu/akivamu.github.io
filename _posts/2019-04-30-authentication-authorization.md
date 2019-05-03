---
layout: post
title: Authentication and Authorization
tags:
- Security
---

## Intro

https://stackoverflow.com/questions/30960315/best-practices-for-roles-vs-claims-in-asp-net-identity

Authentication: who you are?

Authorization: what you are allowed to do?

Identity: the data model describes an entity/user

Claims: the properties/attributes of an identity.  
Each identity can has many claims.  
Each claim has info:

- Name/Type: type of the claim. e.g. age, social number, gender...
- Value: the value of that claim. e.g. age is 20, gender is male...
- Issuer: the trusted party/organization that can certify the claim is correct and trusted.
e.g. government can certify social number is correct. Facebook can certify the personal info
is correct.

Scopes: define the scope that an access token can access.  
Scope can contain some claims. E.g. `profile` scope can access claims: `name`, `gender`, `birthdate`...

https://developer.okta.com/blog/2017/07/25/oidc-primer-part-1
https://security.stackexchange.com/questions/145776/what-are-openid-scopes-and-claims
