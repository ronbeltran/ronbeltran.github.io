---
layout: post
title: Generating feeds per tag in Django
---

**Adding Feeds**

One of the nice features of a website is the ability to have RSS or Atom feeds for say articles, blog entries, photos or links that you newly published. If your website is made with Django life gets easier, it has a built-in module [django.contrib.syndication](www.djangoproject.com/documentation/syndication_feeds) that handle basic syndication functionality.

Let's create a feed for our imaginary web app (a blog):

Inside our web app **blog** create a new file **feeds.py**

{% highlight python %}
    from django.core.exceptions import ObjectDoesNotExist
    from django.utils.feedgenerator import Atom1Feed
    from django.contrib.sites.models import Site
    from django.contrib.syndication.feeds import Feed

    from blog.models import Entry

    current_site = Site.objects.get_current()

    class LatestEntriesFeed(Feed):
        title = 'Latest Entries for %s' % current_site
        link = '/feeds/latest/'

        description = 'Latest entries posted.'
        
        def items(self):
            return Entry.live.all()[:10]

        def item_pubdate(self, item):
            return item.pub_date

        def item_title(self, item):
            return item.title

        def item_description(self, item):
            return item.excerpt_html

        def item_categories(self, item):
            return [c.title for c in item.categories.all()]

        def item_guid(self, item):
            return "tag:%s,%s:%s" % (current_site.domain,
                item.pub_date.strftime('%Y-%m-%d'),
                item.get_absolute_url())
{% endhighlight %}

What the above codes does is that it will output the latest 10 live entry items, you can always adjust items(self) method. Generating the feeds per tag is now simply sub classing **LatestEntriesFeed** and overriding some functions and variables, I use **django-tagging** so the **Tag** object must be imported see below:

{% highlight python %}
    from tagging.models import Tag, TaggedItem

    class TagFeed(LatestEntriesFeed):
        def get_object(self, bits):
            if len(bits) != 1:
                raise ObjectDoesNotExist
            return Tag.objects.get(name__exact=bits[0])
        
        def title(self, obj):
            return "%s: Latest entries under the tag '%s'" \
            % (current_site.name, obj.name)

        def description(self, obj):
            return "%s: Latest entries under the tag  '%s'" \
            % (current_site.name, obj.name)

        def items(self, obj):
            return TaggedItem.objects.get_by_model(Entry, obj.name)
{% endhighlight %}

The next thing to do after defining our feed classes is to register this feed in project root **urls.py**:

{% highlight python %}
    from blog.feeds import LatestEntriesFeed, TagFeed
    feeds = {
        'latest': LatestEntriesFeed,
        'tag': TagFeed,
    }
{% endhighlight %}

and add these inside **urlpatterns**

{% raw %}
    (r'^feeds/(?P<url>.*)/$', 'django.contrib.syndication.views.feed', \ 
        {'feed_dict': feeds}, ),
{% endraw %}

Finally we need to set up the templates **feeds/latest_title.html** and 
**feeds/latest_description.html**, and for tags **feeds/tag_title.html** and 
**feeds/tag_description.html** inside the **templates** directory.

feeds/latest_title.html

{% raw %}
    {{ obj.title }}
{% endraw %}

feeds/latest_description.html

{% raw %}
    {{ obj.body_html|truncatewords_html:"50"|safe }}
{% endraw %}

feeds/tag_title.html

{% raw %}
    {{ obj.title }}
{% endraw %}

feeds/tag_description.html

{% raw %}
    {{ obj.body_html|truncatewords_html:"50"|safe }}
{% endraw %}

That's it, you can also create feeds per categories.

