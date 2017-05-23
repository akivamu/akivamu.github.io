---
layout: post
title: Let's Encrypt HTTPS with NGINX setup
tags:
- Linux
---

This is simplified steps, for full setup, refer to this [tutorial](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04).

Assumed NGINX is installed and running.

## Step 1: Install Let's Encrypt Client
This is a helper to ease the installing process:  
`apt-get install letsencrypt`

## Step 2: Obtain an SSL Certificate
In order to get cert, Let's Encrypt needs to validate our web server, by requesting some information from our server.
And we use LE client to create that information, so prepare a location.  

### Prepare
Supposed we have NGINX serving a site at `/var/www/html`. So make it accessible from outside, by editing
`/etc/nginx/sites-available/default` (or your exposed site):  
```
location ~ /.well-known {
  allow all;
}
```
### Request certificate
`letsencrypt certonly -a webroot --webroot-path=/var/www/html -d example.com -d www.example.com`

### Result
Certificate will be obtained and stored in `/etc/letsencrypt/live/example.com`

## Step 3: Configure NGINX to use certificate
Edit `/etc/nginx/sites-available/default` like this:
```
server {  
    ...  
    listen 443 ssl;  
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;  
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;  
    ...  
}
```

## Renew certificate
`letsencrypt renew`

You may want to setup a cron task to schedule auto renewal