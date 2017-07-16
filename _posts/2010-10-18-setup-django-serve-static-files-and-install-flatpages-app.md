---
layout: post
title: Setup Django to Serve Static Files and Install Flatpages App
---

This is the continuation of post in our series "Dive into Django". In our previous post [Django Creating the Home Page](/2010/10/django-creating-home-page.html), we already defined our base template, written our home page handler and tied it with a url mapping, we also create some css code to make our template design more beautiful. Now let's add some url mapping so that Django can serve static files during development.

In the project root directory open **urls.py** and add at the bottom:

{% highlight python %}
    import settings

    if settings.DEBUG:
        urlpatterns += patterns('',
        # Serve static media
        (r'^media/(?P<path>.*)$', 'django.views.static.serve',
            {'document_root': 'static'}, ),
        )
{% endhighlight %}

This code blocks tells Djang to serve static files in development that is when DEBUG is True and look for static files in **static** directory. For example this will handle requests such as:

- /media/css/style.css
- /media/images/logo.png
- /media/js/jquery.js

You can now navigate to **http://localhost:8000** and see our base django website structure. Let's add the [flat pages app](http://docs.djangoproject.com/en/dev/ref/contrib/flatpages/#module-django.contrib.flatpages).

**Installing Flatpages App**

A flatpage is a simple object with a URL, title and content. Use it for one-off, special-case pages, such as **About** or **Privacy Policy** pages, that you want to store in a database but for which you dont want to develop a custom Django application. A flatpage can use a custom template or a default, systemwide flatpage template. It can be associated with one, or multiple, sites. From Django website follow the installation instruction [here](http://docs.djangoproject.com/en/dev/ref/contrib/flatpages/#installation).

**Create the Flatpage templates**

Change into project root **templates** folder and create a new folder **flatpages**. Inside **flatpages** folder make a new file **default.html**. Open **default.html** and add the ff:

{% raw %}
    {% extends "base.html" %}
    {% block title %}{{flatpage.title}}{% endblock %}
    {% block content %}
    {{flatpage.content}}
    {% endblock %}
{% endraw %}

You can now view the flatpages you've created. This is exactly how this site is made. Please consult the [Django Documentation](http://docs.djangoproject.com/en/1.2/) for more info. Enjoy.
