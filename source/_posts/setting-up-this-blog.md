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

```bash
$ sudo apt update
$ sudo apt install -y nginx
```

I made sure to allow HTTP (port 80) and HTTPS (port 443) traffic on my VPS through the firewal. This is accomplished by configuring UWF (Uncomplicated FireWall).

Snippet from the man page for UFW.
```
NAME
       ufw - program for managing a netfilter firewall

DESCRIPTION
       This program is for managing a Linux firewall and aims to provide an easy to use interface for the user.

```

When writing this blog post I realized I didn't have the man page utility installed on my VPS. Embarassing, I know.

If you don't have UFW installed you can do so as follows:
```
$ sudo apt install -y ufw man
```

I went through the steps and the longest one was generating the strong Diffie-Hellman ([wiki](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) & [stackexchange](https://security.stackexchange.com/questions/45963/diffie-hellman-key-exchange-in-plain-english)) group. It took a few minutes as it had to generate a strong prime number of the largest kind.

This generates a private key stored in `/etc/ssl/private/nginx-selfsigned.key` with contents looking like this:

```
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAnX7LZWN/neuGtEAkeLnepJAzlIeRfeGiu4Dkiuxfgw/cNVxE
u5VRywiWpL5L3NB8QrKqjzI+/PFdNeOtXusogLTyYncLUMncubnyCVfSEvmwLc3q
zkm6AdouW1w54BGWBgXf1CoCHdMn4t/MRv0oykgsxWu6o1ZlFWPLP7ExrdORUYz/
jDJvUnD9v8CqFUjBNNjjGM32dA+uM+YrIIBJgt48glRy3zLOwExWyS6QrsYzzmIJ
GUUcSO7gL+gi5hUU8KGZeXL4dGpeIyVUTuQT8ysper/+cgX1EVwajQN5Jk/fDYL+
iBK7QGSIRvC5gIfOiHiAvVl41RcRuazGvjGKUQIDAQABAoIBAFcudvz65QvP8tMY
ppfFm/9Qa50OpnUeT+ynXtugGQb94pIPL2T/NTXE3U74oBnttrtdF5kI4WE3YreP
/sqKNfN5NqtW5oakF3EgA2E0oe8rFGFmmVHz8elQk1Szar4kPf27hrl1P5Ku7mdi
Jzwsq0W+SjpGdhwwmOgMQ9hQCAYGNSXK2hkKDn1iiXzG84RJ5LhDzXYTirPVoLMX
SzjusDzsyUjVW9CNaQHuEeiWEBh6H2KHuBbSD0GmufnwpynKcDOr0rsPHaSEstd2
89VkpRx2ztuMXz3AeBE9O/kNp5fNXaiWDlNbC2x149w2wqXworBEh+PW0y1LXcfP
zcRx7qECgYEA5WYhSe7nrISkBvs4pEA//fLWcjANYifCdu1Vzbb+au58IDlkPJHk
johzaGwzJLl1mOcacGHuE5TRKkWRIk9HQNiFdYKJqEnra0dfre97udOfbDsoJNz1
FmPNYHB/oZlApK8ugGkXUL37pCOdrxXtY2X+BqqdlyJBD85/g726tvUCgYEAr8In
VOIQk019bgBMPK57JLVo57N+9htwc/GA5QKxaJ+5Gl5CRqjrj2JK2rLnWQ6MPAeX
WJiO1lJE5D6Ty1nPNi4uzzjvJqz4aWT34kROjKtEeOGh9qs2LCIqgBb/1hnnHzqj
wqF92/SBh9Yw7+DrZVPokrZX94RNALJU5Qi8lG0CgYBbnP78ZNHXHx3Wv+uuBQ7J
X3rvHWuq4JAUKNKUllli4R1nTSRP4D/0AGB+un9uUja5usnfSeznmw4xoJjGp/yf
1BjwhhJtyB+ht15pu3OhEffgJHvyxN+IcpEpy6PdKLQdUOz0UYQDNexSb/rgWpR0
lt3tUaQgIIj1RBW+yj3QJQKBgHqVUrNcPK1QVZQ8Nu/3zcGLmVBJG9rwxlzHatvP
JgdPp12hsl3wAONvbb/dGYg+okZYERHkzXLjzFLY5mHlEKW8Tj+PeRUjfNqdZlp0
w8hOx6H71OfjABSF+9h8shEU3eMlFgRweATKIvGnzVfxKpzwifyRmwbySnaa6qUX
k9d9AoGAFo4n+zH3CKsgrRObkQvE8gnTVJBk1SoKDzt92VivmuJGSYDZAO+4+g1z
Mbt0sVmJZdWvaxaNSKPkY4RYaANEE19+UiXhcetUJj7msWEeATuX7ZjKRSXZKtgL
dOWX5mL7eaYMUTZBh6dIdR0TlEZem+Z7D2gf/YUQQSYYyN/sIl0=
-----END RSA PRIVATE KEY-----
```
Obviously the private key you see is not the one I generated.

This also generates a certificate file stored in `/etc/ssl/certs/nginx-selfsigned.crt` with the contents:
```
-----BEGIN CERTIFICATE-----
MIIDBzCCAe+gAwIBAgIJALyNBFSEa3W0MA0GCSqGSIb3DQEBBQUAMBoxGDAWBgNV
BAMMD3d3dy5leGFtcGxlLmNvbTAeFw0xOTAxMTYyMjUzMDdaFw0yOTAxMTMyMjUz
MDdaMBoxGDAWBgNVBAMMD3d3dy5leGFtcGxlLmNvbTCCASIwDQYJKoZIhvcNAQEB
BQADggEPADCCAQoCggEBAPX7ScJkzPm8vkLi3EDyZFRzQz3JkGl9O/y7EQXn7fay
YlNP+FJ8FMklor9VAWR1rPUiB3LYJI67h8RmUo7hNAYVbmvssHTfuF/tuaEK9uId
U9mjOAd7zLO5UEoEycTUOoTANsukrczheJj7xq8nO++ym8JhVxku/FzgclhvlwNY
nUqRGBSiN2efX+UrAgjgfVQmw2D/iJzO3BKpJ7kuYoeEdGstPho+MaZf3blujHNh
LZY1BRNNzaboqj3bk6kI3PqZDvUuMBCmkURFDsuJegNdtkufi7zwrf0kSA4VrDvg
XWWNCjwR1mQfkXB8lvJf3fmieLCzI6+wKH9I5smPgd0CAwEAAaNQME4wHQYDVR0O
BBYEFN74XSMjmtZcxWO8jJaBJjEGIB1nMB8GA1UdIwQYMBaAFN74XSMjmtZcxWO8
jJaBJjEGIB1nMAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEFBQADggEBAEPp2HST
A90WdQbzJXwnNtmT2f/YCuXlUzxV2ewNGIYI7sSqaF7MwEifV7yAcSvJ3+733qRr
iL3EIkIrzN4nmkhLfr1+1+Yf084RUbEoODSt6fXDFNJiVZZR6l+llVPXlwT70PDp
rnT+pWXZuh5H3r6fl5ALcyEi+EDLe9PWzE9vnbm50VLfqexhJ876T941of16OyXZ
1oK2VrFpnazqXUllbOKOKUxdWMs5c8FcKqBqX0ucaCmnlD27XoC4ormR4s8uTNWU
Ttjum0qmDLgJ4AUNaWY+LEXfxIlZG7tubf/e5+wiZPGoYVslnlFa8pQf6yDXGSiR
V69Y2+TZoTAULgU=
-----END CERTIFICATE-----
```
Again, not the real value.

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
The `/etc/nginx/snippets/self-signed.conf` file contains:
```
ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
```

The `The `/etc/nginx/snippets/ssl-params.conf` file contains:
```
ssl_protocols TLSv1.2;
ssl_prefer_server_ciphers on;
ssl_dhparam /etc/nginx/dhparam.pem;
ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384;
ssl_ecdh_curve secp384r1; # Requires nginx >= 1.1.0
ssl_session_timeout  10m;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off; # Requires nginx >= 1.5.9
ssl_stapling on; # Requires nginx >= 1.3.7
ssl_stapling_verify on; # Requires nginx => 1.3.7
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;
# Disable strict transport security for now. You can uncomment the following
# line if you understand the implications.
# add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
add_header X-Frame-Options DENY;
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";
```
I didn't really diverge much frmo the recommendations found in the tutorial.

Once all that jazz is done, you have to "enable" your site in the eyes of NGINX by making a symbolic link:
```
$ sudo ln -s /etc/nginx/sites-available/api.muka.io /etc/nginx/sites-enabled
```

Check your NGINX configuration with `sudo nginx -t` and then finally restart the NGINX service `sudo service restart nginx`.

Once you're operational open your favourite browser (I used Google Chrome) and prepare to see how paranoid it gets. See below.


![Paranoid Chrome](/images/paranoid_chrome_2.png "Paranoid Chrome")

![NGINX OK](/images/nginx_setup_ok.png "NGINX OK")

Part DEUX of this fun, fun tutorial will take place in another post where I redid all of this but used [Let's Encrypt](https://letsencrypt.org/).