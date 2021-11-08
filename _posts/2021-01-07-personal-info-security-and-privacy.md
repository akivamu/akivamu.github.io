---
layout: post
title: One approach to secure personal info
tags:
- Security
---

When living in internet world, we have too many info to secure. Let's separate into 3 levels.

# 1. Real life profile

This is profile that links to your real life identity.

Create one primary email for official services:
- Government and organizations registration (banks, shops, telco...)
- Social networks linked to real life id
- As a contact for real life
- Daily basic services: mobile login, contact sync...

All these accounts must be MFA-protected, use password manager.

# 2. Anonymous profile

This is a fake profile, must NOT have any connection to real life profile. Common use cases:
- Anonymous accounts (gaming, social).
- Online-only registrations (don't require real life id verification), like crypto wallets

All these accounts must be MFA-protected, use password manager.

Note: #1 and #2 are usually used in daily basic, so be careful if you accidental make a connection between them.

# 3. Secret profile for backup

This is where you store your most secret and important info that can't be lost, like password manager data (encrypted).

Important requirements:
- Permanent: this storage must be high avaible, redundancy backup.
- Public accessible: in case you lost everything else, you can login to this profile and recover data.
- Secured login: MFA-protected. Keep MFA key where it is 100% safe:
  - A public permanent git services
  - Physically: on a paper, tattoo
- Encrypted: all data stored here must be strongly encrypted.

Note:
- Do not use this account other than to store secrets.
- Only use/login to this account when really needed.
