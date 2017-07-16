---
layout: post
title: Deploying Django Apps using Nginx and Apache mod_wsgi Part 2
---

This is the continuation of our post [Deploying Django Apps using Nginx and Apache mod_wsgi Part 1](/2010/10/deploying-django-apps-using-nginx-and-apache-mod_wsgi-part-1.html). In this second part we will be configuring Nginx to serve static files and route request to our wsgi process.

**Install Nginx**

    sudo aptitude install nginx

Nginx configuration can be found in **/etc/nginx/nginx.conf** and you should be familiar with its configuration read here [Nginx Config](http://wiki.nginx.org/NginxConfiguration).
Below is my sample config:

    user www-data;
    worker_processes  2;

    error_log  /var/log/nginx/error.log;
    pid        /var/run/nginx.pid;

    events {
        worker_connections  1024;
        # multi_accept on;
    }

    http {
        include       /etc/nginx/mime.types;
        access_log  /var/log/nginx/access.log;
        sendfile        on;
        #tcp_nopush     on;
        #keepalive_timeout  0;
        keepalive_timeout  65;
        tcp_nodelay        on;
        gzip  on;
        gzip_comp_level 2;
        gzip_proxied any;
        gzip_types text/plain text/css application/x-javascript text/xml application/xml application/xml+rss text/javascript;
        gzip_disable "MSIE [1-6]\.(?!.*SV1)";

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
    }

**Setup our site with Nginx**


    /etc/nginx/sites-available/www.example.com

    upstream django {
        # Apache/mod_wsgi running on port 8000
        server 127.0.0.1:8000;
    }
    
    server {
        listen xxx.xx.xx.xxx:80; # Modify this to be your servers ip address.
        server_name example.com;
        rewrite ^/(.*) http://www.example.com/$1 permanent;
    }

    server {
        listen xxx.xx.xx.xxx:80;
        server_name www.example.com;

        # serve media
        location ~ ^/media/(.*)$ {
             alias  /var/www/www.example.com/static/$1;
             #access_log  /var/www/www.example.com/logs/direct.log;
             access_log off;
             expires max;
        }

        location = /favicon.ico {
             alias  /var/www/www.example.com/static/favicon.ico;
        }

        location / {
        include /etc/nginx/proxy.conf;
        index  index.html index.htm;
        root /var/www/www.example.com/public_html;
        access_log off;

        if (!-f $request_filename) {
            proxy_pass http://django;
            break;
        }
    }

    }

This config routes the dynamic requests to our apache mod_wsgi process listening in http://127.0.0.1:8000. We also route request from example.com to 
www.example.com. We also have **include /etc/nginx/proxy.conf** a basic proxy configuration, If you want to know more about this read the nginx documentation.

    proxy_redirect     off;
    proxy_set_header   Host             $host;
    proxy_set_header   X-Real-IP        $remote_addr;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;

    client_max_body_size       10m;
    client_body_buffer_size    128k;

    proxy_connect_timeout      90;
    proxy_send_timeout         90;
    proxy_read_timeout         90;

    proxy_buffer_size          32k;
    proxy_buffers              4 32k;
    proxy_busy_buffers_size    64k;
    proxy_temp_file_write_size 64k;

Enable the virtual hosts and we're done:

    sudo ln -s /etc/apache2/sites-available/www.example.com /etc/apache2/sites-enabled/www.example.com

and don't forget to reload Nginx config:

    sudo /etc/init.d/nginx reload

That's it, you have now deployed a django app using Apache mod_wsig and front-end Nginx.

**Problem Encountered**

If you had checked the Apache access logs you will notice that all incoming request came from the IP address 127.0.0.1, this is the result of nginx forwarding a dynamic request to http://127.0.0.1:8000. To solve this problem we need another Apache module  **libapache2-mod-rpaf** for the real IP of the incoming request to be visible from Apache.

    sudo aptitude install libapache2-mod-rpaf
    sudo a2enmod rpaf
    sudo /etc/init.d/apache2 force-reload

After you run those commands correctly, incoming requests/connections were logged correct and real IP address will be visible from Apache.
