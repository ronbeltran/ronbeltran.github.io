---
layout: post
title: "Setup Cloudflare Free SSL Certificates"
date: 2019-09-14 22:06:19 08:00
---

Today I learned about how to setup a free Cloudflare SSL/TLS certificates in **Nginx** and **Apache2** web servers.

In one of our web projects, we have an expiring paid SSL/TLS certificate that we need to renew.
So we have two options: either we pay the renewal fee or explore other free ssl certificates providers.

We proceed with the latter. We looked at **Lets Encrypt**, it's a great service that provides free, automated and
open Cerificate Authority (CA), but then decided to  use the free TLS/SSL certificate offered by Cloudflare
since we are already using them as our DNS provider.

Below is our sample implementation. Visit their official docs for more information.
https://support.cloudflare.com/hc/en-us/articles/115000479507

## Apache Server Configuration

    SSLEngine On
    SSLCertificateFile /etc/ssl/certs/cloudflare_origin_ca.cert
    SSLCertificateKeyFile /etc/ssl/private/cloudflare_private_key.pem
    SSLCertificateChainFile /etc/ssl/certs/cloudflare-origin-pull-ca.cert

## Nginx Server Configuration

    ssl on;
    ssl_certificate /etc/ssl/certs/cloudflare_origin_ca.cert;
    ssl_certificate_key /etc/ssl/private/cloudflare_private_key.pem;
    ssl_client_certificate /etc/ssl/certs/cloudflare-origin-pull-ca.cert;
    ssl_verify_client on;

[Origin Pull Certificate]: https://support.cloudflare.com/hc/en-us/article_attachments/201243967/origin-pull-ca.pem
