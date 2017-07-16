---
layout: post
title: Run the latest Django version on Google App Engine
description: How to use latest Django on Google App Engine
---

Have you ever wanted to use the latest Django on your app engine projects?
Yes? Then you checked that the supported Django versions in
[Third Party Libraries in Python 2.7][GAE_DJANGO] were outdated.
In this post I will be showing you how you can setup Django project using
latest version. This technique will also work for the future version of Django.

First is to create our project directory:

    mkdir -pv django_appengine/lib
    cd django_appengine

Install latest Django into our local directory via pip:

    pip install Django -t lib

Check PYTHONPATH and append the `lib` path to it where our local Django lives:

    echo $PYTHONPATH
    export PYTHONPATH=$PYTHONPATH:~/Web/django_appengine/lib

Verify that the `lib` path is added:

    echo $PYTHONPATH

You can now create a Django project via `django-admin.py`:

    python lib/django/bin/django-admin.py startproject mysite .

And you can also create a Django app with `manage.py`:

    python manage.py startapp blog

Next step, hook up the Django project into Google App engine with
`app.yaml` and `appengine_config.py`

### app.yaml

    application: app-id
    version: 1
    runtime: python27
    api_version: 1
    threadsafe: yes

    handlers:
    - url: .*
      script: mysite.wsgi.application

### appengine_config.py

    from google.appengine.ext import vendor
    vendor.add('lib')

Some minor adjustments though, the generated django project has some settings
pre configured we need to modify that since we're not using it in App Engine.

In `settings.py` make the following changes:

{% highlight python %}
INSTALLED_APPS = (
    # 'django.contrib.admin',
    # 'django.contrib.auth',
    # 'django.contrib.contenttypes',
    # 'django.contrib.sessions',
    # 'django.contrib.messages',
    # 'django.contrib.staticfiles',
)

DATABASES = {}
# DATABASES = {
#     'default': {
#         'ENGINE': 'django.db.backends.sqlite3',
#         'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
#     }
# }
{% endhighlight %}

Also in `mysite/urls.py`:

    urlpatterns = [
        # url(r'^admin/', include(admin.site.urls)),
    ]

Run the project and open http://localhost:8080/ in your browser:

    dev_appserver.py .

Congratulations! You're now running the latest Django version on App Engine.

### More improvements:

With the release of `Django 1.8` it now supports multiple template engines within the same project.
Yes! We can now flawlessly use Jinja2 templates. Since Jinja2 is included in bundled Python Third Party
packages in App Engine, adding it to our project is easy: Add below in `app.yaml` and follow the steps
[here][DJANGO_JINJA2].

    libraries:
    - name: jinja2
      version: "2.6"

[GAE_DJANGO]: https://cloud.google.com/appengine/docs/python/tools/libraries27#django
[DJANGO_JINJA2]: https://docs.djangoproject.com/en/1.8/topics/templates/#django.template.backends.jinja2.Jinja2
