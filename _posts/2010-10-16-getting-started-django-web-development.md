---
layout: post
title: Getting started in Django web development
---

Every time I start to write a Django based website I always start from **django-admin startproject** command. As a developer this repetitive task is annoying and breaks the DRY (Dont Repeat Yourself) principle so I would like to document the process and create a base django site or a site skeleton so that in my next project It lessen my development time.

**Development Checklist**

Python must be already installed and at least version 2.5 or higher since we're going to use SQLite for our development database. SQLite is included in Python 2.5 so you dont need to install the SQLite module. As of time of writing the current stable release version is Django 1.2.3. After you install the requirements, suppose that we want to start a CMS project, we can now start a django project like this:


{% highlight python %}
    django-admin.py startproject cms  
{% endhighlight %}

This creates a **cms** directory called a project root directory. Django creates some files on it. 

- settings.py  
- manage.py  
- urls.py  
- \_\_init\_\_.py  

If you want to know more about those files visit this link:
[http://docs.djangoproject.com/en/1.2/intro/tutorial01/](http://docs.djangoproject.com/en/1.2/intro/tutorial01/).

**Let's Assign a Database**

Open **settings.py** in the project directory using your favorite text editor, and find code blocks below:  
Note: comments are removed


{% highlight python %}
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.', 
            'NAME': '',                      
            'USER': '',                      
            'PASSWORD': '',             
            'HOST': '',                      
            'PORT': '',                      
        }
    }
{% endhighlight %}


change it to this:  


{% highlight python %}
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3', 
            'NAME': 'cms.sqlite3',                      
            'USER': '',                      
            'PASSWORD': '',             
            'HOST': '',                      
            'PORT': '',                      
        }
    }
{% endhighlight %}


**Activate the Admin Interface**

Find the blocks below:


{% highlight python %}
    INSTALLED_APPS = (
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.sites',
        'django.contrib.messages',
        # Uncomment the next line to enable the admin:
        # 'django.contrib.admin',
        # Uncomment the next line to enable admin documentation:
        # 'django.contrib.admindocs',
    )
{% endhighlight %}


change it to:


{% highlight python %}
    INSTALLED_APPS = (
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.sites',
        'django.contrib.messages',
        # Uncomment the next line to enable the admin:
        'django.contrib.admin',
        # Uncomment the next line to enable admin documentation:
        # 'django.contrib.admindocs',
    )
{% endhighlight %}


then Open **urls.py** in project root. The code will be like this:


{% highlight python %}
    from django.conf.urls.defaults import *

    # Uncomment the next two lines to enable the admin:
    # from django.contrib import admin 
    # admin.autodiscover()
    urlpatterns = patterns('',
        # Example:
        # (r'^cms/', include('cms.foo.urls')),

        # Uncomment the admin/doc line below to enable admin documentation:
        # (r'^admin/doc/', include('django.contrib.admindocs.urls')),
        # Uncomment the next line to enable the admin:
        # (r'^admin/', include(admin.site.urls)),
    )
{% endhighlight %}

Uncomment some line so that it would look like this:

{% highlight python %}
    from django.conf.urls.defaults import *

    # Uncomment the next two lines to enable the admin:
    from django.contrib import admin 
    admin.autodiscover()

    urlpatterns = patterns('',
        # Example:
        # (r'^cms/', include('cms.foo.urls')),
        # Uncomment the admin/doc line below to enable admin documentation:
        # (r'^admin/doc/', include('django.contrib.admindocs.urls')),

        # Uncomment the next line to enable the admin:
        (r'^admin/', include(admin.site.urls)),   
    )
{% endhighlight %}

**Run the development server**

Everything looks good now, before we see the site in our browser there are still other simple things to do. 
We still need to sync the database. Run this command:  

{% highlight python %}
    python manage.py syncdb
{% endhighlight %}

It will ask to make an administrator account for the site, enter 'yes' and provide username and password. Don' forget those inputs because you need it to login to admin area. Django provides us a development server so that we dont need to setup a complicated server for our development project.

{% highlight python %}
    python manage.py runserver
{% endhighlight %}

By default the development server listen at port 8000. So type in your browser **http://localhost:8000**, you will see a 404 not found error message since you have not yet coded the root url view and you can also go to admin here **http://localhost:8000/admin**, enter your username and password.
