---
layout: post
title: Django Templates and Template Inheritance
---

This is a continuation of the previous post [Getting Started in Django web development](/2010/10/getting-started-django-web-development.html) we already activated the admin interface and configure our development database. Now in this part we will tackle the feature called template inheritance. It will be an exciting process since it will be the part that we will design the look and feel of our Django web site. Without much further a do lets begin.

**Preparing Templates and Static directories**

Let's first prepare some directories to make things more organized. Create two new directories in the project root along side **settings.py** 

{% highlight bash %}
    cd cms
    mkdir -pv static/{css,js,images} templates
{% endhighlight %}

We will store our html templates in **templates** directory and the css, js and images to **static** directory. This will make our file more structured. Tip. It would be best if you read first the official Django documentation on [Templates](http://docs.djangoproject.com/en/1.2/#the-template-layer)

**Templates and Template Inheritance**

A **template** is simply a text file that can generate any text based formats such as HTML, XML, CSV, etc. It also contains **variables** that is replaced with values when the template is evaluated, and **tags**, which control the logic of the template. Below is a minimal example:

{% raw %}
    {% extends "base.html" %}
    {% block title %} Home {% endblock%}
    {% block content %}
        <p>Welcome to home page.</p>
    {% endblock %}
{% endraw %}


What happens when a view returns this template is that through **template inheretance** this template inherits the layout of the **base.html** and replaces the blocks with its value.

**Creating a Theme HTML Structure**

**Ian Stewart** made a very good tutorial on creating a Wordpress theme and since some of those concepts are very much applicable to other web development genre, we will take advantage of those resources and his advice for two goals in coding a web site: **lean code** and **meaningful code**. Read more his blog article [Creating a WordPress Theme HTML Structure](http://themeshaper.com/creating-wordpress-theme-html-structure-tutorial/). Ian I would love to send a pingback/trackback to your article but I think before I can do that I have to code pingback or trackback functionality in my Django based site. 

Let's take a look and examine this piece of html code. We may notice that it contains 3 main divs, 2 widgets after the main content. This kind of html structure will let us shape the layout with just few lines of CSS.

{% highlight html %}
    <html>
    <head><title></title>
    </head>
    <body>
    <div id="wrapper" class="hfeed">    
    <div id="header">    
    <div id="masthead">
      <div id="branding">
      </div><!-- #branding -->
      <div id="access">
      </div><!-- #access -->
    </div><!-- #masthead -->
    </div><!-- #header -->
    <div id="main">
    <div id="container">
      <div id="content">
      </div><!-- #content -->
    </div><!-- #container -->
      <div id="primary" class="widget-area">
      </div><!-- #primary .widget-area -->
      <div id="secondary" class="widget-area">
      </div><!-- #secondary -->
    </div><!-- #main -->    
    <div id="footer">
    <div id="colophon">
       <div id="site-info">
       </div><!-- #site-info -->
    </div><!-- #colophon -->
    </div><!-- #footer -->
    </div><!-- #wrapper -->
    </body>
    </html>
{% endhighlight %}

With these raw html structure let's convert it to a Django base template and we will call it **base.html**. Save it to **templates** directory. It would be like this:

{% raw %}
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml" lang="en-US" dir="ltr">
    <head profile="http://gmpg.org/xfn/11">
    <title>{%block title%}{%endblock%} | Web Python</title>
    {% block meta %}{% endblock%}
    <link rel="stylesheet" type="text/css" href="/media/css/style.css" />
    {% block extrastyle %}{% endblock%}
    </head>
    <body>
    <div id="wrapper" class="hfeed">
    <div id="header">
    <div id="masthead">
      <div id="branding">
        <div><h1 class="site_branding">
        <a href="{% url home_page %}">Web Python</a>
        </h1></div>
        <div><h2 class="site_tagline">Ramblings on Python Web Frameworks</h2></div>
      </div><!-- #branding -->
       <div id="access" >
       </div><!-- #access -->
    </div><!-- #masthead -->
    </div><!-- #header -->
    <div id="main">
    <div id="container">
    <div id="content">
      {%block content%}{%endblock%}
    </div><!-- #content -->
    </div><!-- #container -->
    <div id="primary" class="widget-area">
    <h2 class="heading">Primary</h2>
    {%block primary%}{%endblock%}
    </div><!-- #primary .widget-area -->
    <div id="secondary" class="widget-area">
      <h2 class="heading">Secondary</h2>
      {%block secondary%}{%endblock%}
    </div><!-- #secondary -->
    </div><!-- #main -->
    <div id="footer">
    <div id="colophon">
      <div id="site-info">
      <p>Copyright 2010 <a href="/">Web Python</a></p>
        {%block footer %}{%endblock%}
      </div><!-- #site-info -->
    </div><!-- #colophon -->
    </div><!-- #footer -->
    </div><!-- #wrapper -->
    </body>
    </html>
{% endraw %}

Let's pause and notice the placeholder code blocks that we defined:

{% raw %}
- {% block title%}{% endblock %} - page title here
- {% block meta %}{% endblock %} - meta tag here, good for SEO
- {% block extrastyle %}{% endblock %} - we link css and or js here
- {% block branding %}{% endblock %} - site branding
- {% block primary %}{% endblock %} - we may display Pages, Search form, recent
- {% block secondary %}{% endblock %}  comments  or whatever you want here to appear.
- {% block footer %}{% endblock %} - site footer
{% endraw %}

Whew! This article is becoming long, let's stop right here. I will be discussing how these template fit together with a view to create the home page in my next post. 
