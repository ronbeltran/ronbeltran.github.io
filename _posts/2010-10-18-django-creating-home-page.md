---
layout: post
title: Django Creating the Home Page
---

This is a continuation of the previous post [Templates and Template Inheritance](/2010/10/django-templates-and-template-inheritance.html). In the previous post we had defined our base html layout of our site. We also needed some css code to shape the layout accordingly. We can put our CSS code in the head of **base.html** file but it's much better idea to separate css and put it in external file, along with javascripts and images that is referenced from the base.html file. 

**Creating the Home Page**

Before we can use the templates to render the layout of the page we still need to define **views** to process the request. Create a new file **views.py** in the project root directory. Enter the following codes on it:  

{% highlight python %}
    from django.shortcuts import render_to_response
 
    def home_page(request):
        return render_to_response(
            'home.html',
        )
{% endhighlight %}

Read more by clicking [render_to_response](http://docs.djangoproject.com/en/1.2/topics/http/shortcuts/). 

This python function **home_page** will be called once a web request satisfies the url it is assigned to. What this function do is that it imports **render_to_response** from django.conf , look for the file **home.html** in the **templates** directory, process **home.html** and renders it to the browser screen. 

**Home Page URL**

Open **urls.py** in project root directory, we need to add the url handler for home page. Add the below code inside **urlpatterns**:

{% highlight python %}
    # Home handler
    url(r'^$', 'cms.views.home_page',name='home_page'),
{% endhighlight %}

If you run the development server and visit **http://localhost:8000** you will surely get the error **TemplateDoesNotExist at /** because we still need to create **home.html** so let's write that:

Inside **templates** directory create a new file **home.html** and fill it with these codes:

{% raw %}
    {% extends "base.html" %}
    {% block title %} Home {% endblock%}
    {% block content %}
    <h2>Python Web</h2>
    <p>Welcome to home page.</p>
    {% endblock %}
{% endraw %}

This template will inherit the layout of base.html with **extends** tag. Also will override some values to base.html code blocks, here in our case we override **title** and **home**. 

**Necessary Settings**

By now, Django still doesn't know where to look for our templates. To do that we need to edit **settings.py** and provide values on some fields. Open it and add this at the top:

    import os

Then, change MEDIA_URL to
     
    MEDIA_URL = '/media/'

and ADMIN_MEDIA_PREFIX to

    ADMIN_MEDIA_PREFIX = '/media/admin/'

We also need to set this:

{% highlight python %}
    TEMPLATE_DIRS = (
        os.path.join(os.path.abspath(os.path.dirname(__file__)), 'templates'),
    )
{% endhighlight %}

This tells django that all our template files are located inside this **templates** directory. This is a relative path. Why we're using relative path instead of hard coding absolute paths? The answer is simple: Since we're still under development placing **templates**, **static** directories and even our SQLite database file inside the project root provide us with flexibility. You can just copy the entire project folder and run it on some other computer with Python and Django installed and you're back in your development.

You can now run the development server and navigate to **http://localhost:8000**. You will see the welcome home page. Verify that the layout of the home page is what you had designed in base.html by viewing its source or right click on the browser page and view source.

**Let's put  some CSS**

Ok, we need to shape now the base.html layout with css, going back to Ian's [wordpress theme tutrial](http://themeshaper.com/wordpress-themes-templates-tutorial/), he had already defined several designs for the base.html layout. [Check it out here](http://code.google.com/p/your-wordpress-theme/source/browse/#svn/trunk/styles). This is great for us since it will lessen the development time and some might not be a CSS guru, including myself. LOL. 

I had chosen the design "three columns with right sidebar". Create a new file **static/css/style.css** and add these css:

{% highlight css %}
    /*
    LAYOUT: Three-Column (Right)
    DESCRIPTION: Three-column fluid layout with two sidebars right of content
    */

    #container {
    float: left;
    width: 100%;
    }
    #content {
    margin: 0 400px 0 0;
    }
    #primary, #secondary {
    float: left;
    overflow: hidden;
    width: 180px;
    }
    #primary {
    margin: 0 0 0 -380px;
    }
    #secondary {
    margin: 0 0 0 -180px;
    }
    #footer {
    clear: left;
    width: 100%;
    }
    /* END */

    #wrapper {
    margin: 0 auto;
    width: 960px;
    }

    #branding {
    height: 100px;
    text-align: center;
    color: #fff;
    background-color: #036;
    text-indent: 10px;
    }

    #branding a {
    color: #fff;
    text-decoration: none;
    }

    #branding div h1,h2 {
    padding-top: 10px;
    margin: 0;
    }

    .site_branding {
    font-size: 40px;
    }

    #access {
    padding:0;
    margin:0;
    }

    body,
    input,
    textarea {
    color: #333;
    font-family: sans-serif,"Lucida Grande";
    font-size: 15px;
    }

    h1 { font-size: 15pt; }
    h2 { font-size: 14pt; }
    h3 { font-size: 13pt; }
    h4 { font-size: 12pt; }
    h5 { font-size: 11pt; }
    h6 { font-size: 10pt; }

    #footer {
    margin: 10px auto;
    font-size:8pt;
    padding-top: 10px;
    text-align: center;
    height: 200px;
    }

    #footer a, #footer a:visited {
    color: #036; /* footer link colour */
    text-decoration: none;
    }
{% endhighlight %}

and we need to make this style visible and accessible to base.html. Add these line to base.html head or make sure that it is there.

{% highlight html %}
    <link rel="stylesheet" type="text/css" href="{{MEDIA_URL}}css/style.css" />
{% endhighlight %}

At these point you will not yet able to see the effects of css that we just added because Django by default doesn't serve static files. In the next post we will teach django to serve static files.
