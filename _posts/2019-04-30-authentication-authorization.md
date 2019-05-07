---
layout: post
title: Authentication and Authorization with OAuth and OpenID Connect
tags:
- Security
---

## Intro

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

Reference: [https://developer.okta.com/blog/2017/07/25/oidc-primer-part-1](https://developer.okta.com/blog/2017/07/25/oidc-primer-part-1)

## OAuth

Here OAuth means OAuth 2.

OAuth is a mechanism for delegation of authorization.  
It allow/delegate 3rd apps to access to certain data of user.

There are roles:

- Resource Owner: this is the user
- Client: the app want to access user data
- Resource Server: the server that hold user data
- Authorization Server: the middle man helps user delegate access (via token) to client

References:

- [https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2](https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2)
- [OAuth 2 Simplified](https://aaronparecki.com/oauth-2-simplified/)

OAuth doesn't mean for authentication (i.e. when user logs in to Resource/Authorization server). OAuth doesn't define it.  
Technically, OAuth does not have to give you any information about the user.

OAuth is all about authorization: User delegates to client (give the token) to access his data (limited by `scope`)  
OAuth validates that user has given authorization to client to access some limted data.

But we can use OAuth to [build authentication, but with pitfalls](https://oauth.net/articles/authentication/#common-pitfalls)

## OpenID Connect

OpenID Connect (OIDC) is an authentication layer on top of OAuth 2.0, and standardize things OAuth left:

- Add `id_token` to OAuth
- Standard scopes, claims
- Endpoint discovery
- Client registration

References:

- [OIDC primer](https://developer.okta.com/blog/2017/07/25/oidc-primer-part-1)

## Implementations

[List of implementations of OpenID Connect](https://openid.net/developers/libraries/)

- C#: [IdentityServer4](https://identityserver.io/)
- Javascript: [OIDC provider](https://www.npmjs.com/package/oidc-provider)
- Java: [OpenAM](http://www.forgerock.com/products/open-identity-stack/openam/)
