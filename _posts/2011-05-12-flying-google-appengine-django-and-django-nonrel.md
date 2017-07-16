---
layout: post
title: "Flying on Google Appengine with Django and Django-nonrel"
---

Well I've been busy porting my homemade [Django](http://www.djangoproject.com) based blogging engine to work on [Google Appengine](http://code.google.com/appengine/) thankfully some brilliant guys at [Django-nonrel](http://www.allbuttonspressed.com/projects/django-nonrel) allows us to run some django native apps on the google platform. Django-nonrel makes django run on non-relational databases such as MongoDB, CouchDb, etc. In short it is a **No-SQL support for Django**. Google appengine uses another non-relational proprietary and high-performance database/datastore they called [Big Table](http://en.wikipedia.org/wiki/BigTable), in order for Django to run on appengine [djangoappengine](http://www.allbuttonspressed.com/projects/djangoappengine) was created, it was a great work but expectedly not all features are yet supported.

As you might have observed, or better not, there are no support yet for the following:

- [Tagging](http://en.wikipedia.org/wiki/Tag_%28metadata%29)
- [Date based generic views](http://docs.djangoproject.com/en/dev/ref/generic-views/?from=olddocs#django-views-generic-date-based-archive-index)
- [Categorizing](http://en.wikipedia.org/wiki/Category)

BTW the sites look and feel or theme in here is shamelessly copied from [Wordpress Twenty Ten Theme](http://wordpress.org/extend/themes/twentyten) because I still suck at web design. LOL

And also I'm using [reStructuredText](http://docutils.sourceforge.net/rst.html) to format my blog posts. Not yet planning to use a [WYSIWYG](http://en.wikipedia.org/wiki/WYSIWYG) editor, feature freeze for now to focus on writing some stuff. If you want to leave comments and or violent reactions (please don't), no really if you want to comment or to clarify something HMU below, it's powered by another Django based web app [Disqus Comment System](http://disqus.com/).

Oh I hear Jim asked what can I get from running my web app at google appengine, poor Jim never heard of google platform for web apps since 2008. Seriously, aside from scaling your web apps to serve millions of request with 99% uptime. To be precise google will let your web apps serve up to [5 million requests/month and 1 GB of persistent storage](http://code.google.com/appengine/kb/billing.html). Sounds too good to be true? It is really true. If you exceed that you will have to pay only for what you use. Just like Amazons EC2, it's a cloud service/PaaS.

If you still insist that writing your own blog engine is a waste of time and just reinventing the wheel, I recommend you read this question [Why does every man and his dog want to code a blogging engine?](http://stackoverflow.com/questions/471940/why-does-every-man-and-his-dog-want-to-code-a-blogging-engine).

Now build something usefull.

- Update: I'm rebuilding [my previous site](http://codingpursuit.appspot.com/) from Django-Nonrel in Google App Engine to Jekyll site.
