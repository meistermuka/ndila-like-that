---
title: Setting up this brain dump
date: 2019-01-14 16:05:49
tags:
---

I went through [this](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-on-debian-9) guide to setup SSL on my muka.io domain so this *brain dump* and other subdomains will be secure and ready for when Chrome and other browsers no longer allow you to browse via regular old HTTP.

I found two guides that walk you through the process of setting up and configuring SSL certificates and traffic on your favourite (owned) domain names via NGINX. Here they are:
1. [How To Create a Self-Signed SSL Certificate for Nginx on Debian 9](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-on-debian-9)
2. [How To Secure Nginx with Let's Encrypt on Debian 9](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-debian-9)

### How To Create a Self-Signed SSL Certificate for Nginx on Debian 9 TLDR;
First off, make sure you have NGINX installed and running on your machine. In my case, I have a droplet on Digitalocean running on Debian 9 so I Aptitude the crap out of that installation.

I went through the steps and the longest one was generating the strong Diffie-Hellman ([wiki](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) & [stackexchange](https://security.stackexchange.com/questions/45963/diffie-hellman-key-exchange-in-plain-english)) group. It took a few minutes as it had to generate a strong prime number of the largest kind.

The specific configuration for my [api.muka.io](https://api.muka.io) domain was here: 
```
/etc/nginx/sites-available/api.muka.io
```
Contents:

```
server {
    listen 443 ssl;
    listen [::]:443 ssl;

    server_name api.muka.io;
    include snippets/self-signed.conf;
    include snippets/ssl-params.conf;

    root /var/www/api.muka.io;
    index index.html

    location / {
        try_files $uri/ =404;
    }
}

server {
    listen 80;
    listen [::]:80;

    server_name api.muka.io;

    return 301 https://$server_name$request_uri;
}
```



![Paranoid Chrome](/images/paranoid_chrome_2.png "Paranoid Chrome")

![NGINX OK](/images/nginx_setup_ok.png "NGINX OK")