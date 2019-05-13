---
layout: post
title:  "Generating Large XML Sitemaps in Django"
date:   2018-11-15
categories: django sitemaps
comments: true
---
It was really easy to generate [xml sitemaps][sitemap] using the Django web framework, however If your model data is large it will take a long time building the sitemaps
on the fly. This can result in HTTP 502 Bad Gateway error or proxy timeouts depending in your deployment setup and platform. In our case its an [H12 Request Timeout][request-timeout]
since our django application is deployed in Heroku. Heroku has a hard limit of 30 seconds for a response to be sent upon receiving the web request.

So how to generate sitemaps for a large dataset? One way to solve this problem is to break the large sitemap into a group of smaller sitemaps.
For instance I have built a separate sitemap for Articles by the first letter of the title. So there will be a sitemap file for every letter in the alpahabet.
If you want to use alpahnumeric characters that will also work but in our case alphabet letters works fine. The Python built-in method `ascii_lowercase`
will return an array of alphabet characters that we can loop to build each sitemaps.

{% highlight python %}
from datetime import date
from string import ascii_lowercase
from django.contrib.sitemaps import Sitemap
from .models import Article

def build_sitemaps():
    sitemap = {}
    for char in ascii_lowercase:
        article_sitemap = ArticleSitemap(letter=char)
        sitemap[char] = article_sitemap
    return sitemap


class ArticleSitemap(Sitemap):
    changefreq = "never"
    priority = 0.5

    def __init__(self, letter='a'):
        self.letter = letter.lower()
        super(ArticleSitemap, self).__init__()

    def items(self):
        today = date.today()
        return Article.objects.filter(
            status=Article.PUBLISHED,
            title__istartswith=self.letter,
            ).exclude(
                publish_date__gt=today,
            ).order_by('-publish_date')
{% endhighlight %}

Below I have also added caching of the sitemaps which is really nice. Then add the sitemaps in the `urls.py` like so:

{% highlight python %}
from django.contrib.sitemaps import views as sitemaps_views                                                                                                          
from django.views.decorators.cache import cache_page 

from articles.sitemaps import build_sitemaps

urlpatterns = [
    url(r'^sitemap\.xml$', cache_page(86400)(sitemaps_views.index), {'sitemaps': build_sitemaps()}),                                                                 
    url(r'^sitemap-(?P<section>.+)\.xml$', cache_page(86400)(sitemaps_views.sitemap), {'sitemaps': build_sitemaps()},                                                
        name='django.contrib.sitemaps.views.sitemap'),
]
{% endhighlight %}

[sitemap]: https://docs.djangoproject.com/en/2.1/ref/contrib/sitemaps/
[request-timeout]: https://devcenter.heroku.com/articles/request-timeout 
