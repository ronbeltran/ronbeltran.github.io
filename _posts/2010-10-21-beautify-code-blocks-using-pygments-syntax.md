---
layout: post
title: Beautify code blocks using Pygments Syntax
---

**Install Pre-requisites**

you can install by source or by easy_install / pip:

    easy_install BeautifulSoup Pygments

[Pygments](http://pygments.org/)
It is a generic syntax highlighter for general use in all kinds of software such as forum systems, wikis or other applications that need to prettify source code.

[Beautiful Soup](http://www.crummy.com/software/BeautifulSoup/)
Beautiful Soup is a Python HTML/XML parser designed for quick turnaround projects like screen-scraping.

In your post model save block, add these lines. Of course you still need to replace **self.body_html** with the variable you use in you model.

{% highlight python %}
    soup = BeautifulSoup(self.body_html)
    codeblocks = soup.findAll('pre')
    for block in codeblocks:
        if block.has_key('class'):
            try:
                code = ''.join([unicode(item) for item in block.contents])
                lexer = pygments.lexers.get_lexer_by_name(block['class'])
                formatter = pygments.formatters.HtmlFormatter()
                code_hl = pygments.highlight(code, lexer, formatter)
                block.contents = [BeautifulSoup(code_hl)]
                block.name = 'code'
            except:
                pass
    self.body_html = unicode(soup)
{% endhighlight %}

What the above code does is that it will find all **pre** code blocks using Beautiful Soup parser and determine the lexer by looking at the pre class attribute value which is the syntax type, ie Python, css, html etc. 

**Generate the Pygments CSS**

Suppose that in my template file where I want to high light code blocks I have this:

{% raw %}
    <div class="entry-content" >{{ object.body_html|safe }}</div>
{% endraw %}

Notice the div class **entry-content** must match **get_style_defs('.entry-content')** see below:

{% highlight python %}
    #!/usr/bin/env python
    # python gen_css.py pygments.css
    import sys
    from pygments.formatters import HtmlFormatter

    f = open(sys.argv[1], 'w')

    # You can change style and the html class here:
    f.write(HtmlFormatter(style='colorful').get_style_defs('.entry-content'))

    f.close()
{% endhighlight %}

I also added my **pygments.css** here for reference. Put it in  you **static/css** folder. You should also reference this css only on pages where you want to high light codes to reduce bandwidth usage. You can expose it on say blog pages only. Here in my case I want to reference that css only in my blog pages. In my **entry_detail.html** file:

{% raw %}
    {% block extrastyle %}
    <link rel="stylesheet" type="text/css" href="{{ MEDIA_URL }}css/pygments.css" />
    {% endblock %}
{% endraw %}

{% highlight css %}
    .entry-content .hll { background-color: #ffffcc }
    .entry-content  { background: #ffffff; }
    .entry-content .c { color: #808080 } /* Comment */
    .entry-content .err { color: #F00000; background-color: #F0A0A0 } /* Error */
    .entry-content .k { color: #008000; font-weight: bold } /* Keyword */
    .entry-content .o { color: #303030 } /* Operator */
    .entry-content .cm { color: #808080 } /* Comment.Multiline */
    .entry-content .cp { color: #507090 } /* Comment.Preproc */
    .entry-content .c1 { color: #808080 } /* Comment.Single */
    .entry-content .cs { color: #cc0000; font-weight: bold } /* Comment.Special */
    .entry-content .gd { color: #A00000 } /* Generic.Deleted */
    .entry-content .ge { font-style: italic } /* Generic.Emph */
    .entry-content .gr { color: #FF0000 } /* Generic.Error */
    .entry-content .gh { color: #000080; font-weight: bold } /* Generic.Heading */
    .entry-content .gi { color: #00A000 } /* Generic.Inserted */
    .entry-content .go { color: #808080 } /* Generic.Output */
    .entry-content .gp { color: #c65d09; font-weight: bold } /* Generic.Prompt */
    .entry-content .gs { font-weight: bold } /* Generic.Strong */
    .entry-content .gu { color: #800080; font-weight: bold } /* Generic.Subheading */
    .entry-content .gt { color: #0040D0 } /* Generic.Traceback */
    .entry-content .kc { color: #008000; font-weight: bold } /* Keyword.Constant */
    .entry-content .kd { color: #008000; font-weight: bold } /* Keyword.Declaration */
    .entry-content .kn { color: #008000; font-weight: bold } /* Keyword.Namespace */
    .entry-content .kp { color: #003080; font-weight: bold } /* Keyword.Pseudo */
    .entry-content .kr { color: #008000; font-weight: bold } /* Keyword.Reserved */
    .entry-content .kt { color: #303090; font-weight: bold } /* Keyword.Type */
    .entry-content .m { color: #6000E0; font-weight: bold } /* Literal.Number */
    .entry-content .s { background-color: #fff0f0 } /* Literal.String */
    .entry-content .na { color: #0000C0 } /* Name.Attribute */
    .entry-content .nb { color: #007020 } /* Name.Builtin */
    .entry-content .nc { color: #B00060; font-weight: bold } /* Name.Class */
    .entry-content .no { color: #003060; font-weight: bold } /* Name.Constant */
    .entry-content .nd { color: #505050; font-weight: bold } /* Name.Decorator */
    .entry-content .ni { color: #800000; font-weight: bold } /* Name.Entity */
    .entry-content .ne { color: #F00000; font-weight: bold } /* Name.Exception */
    .entry-content .nf { color: #0060B0; font-weight: bold } /* Name.Function */
    .entry-content .nl { color: #907000; font-weight: bold } /* Name.Label */
    .entry-content .nn { color: #0e84b5; font-weight: bold } /* Name.Namespace */
    .entry-content .nt { color: #007000 } /* Name.Tag */
    .entry-content .nv { color: #906030 } /* Name.Variable */
    .entry-content .ow { color: #000000; font-weight: bold } /* Operator.Word */
    .entry-content .w { color: #bbbbbb } /* Text.Whitespace */
    .entry-content .mf { color: #6000E0; font-weight: bold } /* Literal.Number.Float */
    .entry-content .mh { color: #005080; font-weight: bold } /* Literal.Number.Hex */
    .entry-content .mi { color: #0000D0; font-weight: bold } /* Literal.Number.Integer */
    .entry-content .mo { color: #4000E0; font-weight: bold } /* Literal.Number.Oct */
    .entry-content .sb { background-color: #fff0f0 } /* Literal.String.Backtick */
    .entry-content .sc { color: #0040D0 } /* Literal.String.Char */
    .entry-content .sd { color: #D04020 } /* Literal.String.Doc */
    .entry-content .s2 { background-color: #fff0f0 } /* Literal.String.Double */
    .entry-content .se { color: #606060; font-weight: bold; background-color: #fff0f0 } /* Literal.String.Escape */
    .entry-content .sh { background-color: #fff0f0 } /* Literal.String.Heredoc */
    .entry-content .si { background-color: #e0e0e0 } /* Literal.String.Interpol */
    .entry-content .sx { color: #D02000; background-color: #fff0f0 } /* Literal.String.Other */
    .entry-content .sr { color: #000000; background-color: #fff0ff } /* Literal.String.Regex */
    .entry-content .s1 { background-color: #fff0f0 } /* Literal.String.Single */
    .entry-content .ss { color: #A06000 } /* Literal.String.Symbol */
    .entry-content .bp { color: #007020 } /* Name.Builtin.Pseudo */
    .entry-content .vc { color: #306090 } /* Name.Variable.Class */
    .entry-content .vg { color: #d07000; font-weight: bold } /* Name.Variable.Global */
    .entry-content .vi { color: #3030B0 } /* Name.Variable.Instance */
    .entry-content .il { color: #0000D0; font-weight: bold } /* Literal.Number.Integer.Long */
{% endhighlight %}
