---
layout: post
title: Deploying Django Apps using Nginx and Apache mod_wsgi Part 1
---

There are many ways to deploy a Django app into production as describe here [Deploying Django](http://docs.djangoproject.com/en/1.2/howto/deployment/). However, deploying Django with **Apache** and **mod_wsgi** is the recommended way to get Django into production. **mod_wsgi** is an Apache module which can be used to host any Python application which supports the Python WSGI interface, including Django. Django will work with any version of Apache which supports mod_wsgi. Read detailed documentation here [mod_wsgi](http://code.google.com/p/modwsgi/). Although the recommended way to deploy django is with Apache and mod_wsgi, well  as we know it Apache is a memory hog there will come a time that Apache will eat all your precious memory. Don't worry we can put a remedy for that. We're building a web server here, there are two types of request a web server handles: static requests and dynamic requests. To get a good balance between performance and stability we need to separate dynamic and static requests. Yes, we need to add another server to handle static request and let mod_wsgi handle dynamic request which is the job that it is good at doing. 

**Environment Server Checklist**

Let's assume that we're using the following in our **virtual private server** in production:

- Debian/Ubuntu Linux - server OS
- Apache Web Server - handles dynamic request
- Nginx - front-end proxy and serves static requests
- MySQL - database

If apache is not yet installed, you may install it with:

    sudo aptitude install apache2

and we need to install **mod_wsgi** 

    sudo aptitude install libapache2-mod-wsgi

By default Apache listen in the vps IP address on port 80, lets switch Apache to run on the local host. Open Apache **ports.conf** and edit some lines so that Apache will listen only on localhost and run on port 8000:

    sudo vim /etc/apache2/ports.conf

    NameVirtualHost 127.0.0.1:8000
    Listen 127.0.0.1:8000

And restart Apache:

    sudo apache2ctl graceful
or   
    sudo /etc/init.d/apache2 restart

**Configure Virtual Hosts**

Let's assume that we have this structure:

- /var/www/www.example.com/mysite/ - our django site
- /var/www/www.example.com/static/ - media files css, js, images etc
- /var/www/www.example.com/public_html/ - document root
- /var/www/www.example.com/logs/ - apache and nginx logs

Setup virtual host for www.example.com:(example only)

    sudo vim /etc/apache2/sites-available/www.example.com

and put this content:

    <VirtualHost 127.0.0.1:8000>
    ServerName www.example.com
    ServerAlias www.example.com

    DocumentRoot /var/www/www.example.com/public_html

    <Directory /var/www/www.example.com/mysite>
        Order allow,deny
        Allow from all
    </Directory>

    WSGIScriptAlias / /var/www/www.example.com/mysite/django.wsgi
    ErrorLog /var/www/www.example.com/logs/error.log
    CustomLog /var/www/www.example.com/logs/access.log combined
    </VirtualHost>

Create **django.wsgi** in /var/www/www.example.com/mysite/django.wsgi, this wgi file will map url requests to defined urls in urls.py:

    sudo vim /var/www/www.example.com/mysite/django.wsgi

{% highlight python %}
    import os
    import sys
    sys.path.append('/var/www/www.example.com/mysite')
    sys.path.append('/var/www/www.example.com/')
    os.environ['PYTHON_EGG_CACHE'] = '/var/www/www.example.com/.python-egg'
    os.environ['DJANGO_SETTINGS_MODULE'] = 'settings'
    import django.core.handlers.wsgi
    application = django.core.handlers.wsgi.WSGIHandler()
{% endhighlight %}

Note: If you changed something in the django codes you have to reload apache config for the changes to take effect. We're now done with our virtual host activate www.example.com by issuing this command:

    sudo a2ensite www.example.com

and to disable it

    sudo a2dissite www.example.com

That's it for apache part.
