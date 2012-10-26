.. include:: <s5defs.txt>

=====================================
Web Crawling and Metadata with Python
=====================================

:Author:  Andrew Montalenti
:Date:    $Date: 2012-10-26 09:00:00 -0500 (Fri, 26 Oct) $

.. This document is copyright Andrew Montalenti and Parsely, Inc.

.. container:: handout

    **How this was made**

    This document was created using Docutils_/reStructuredText_ and S5_.

    Introduction to web crawling (using Scrapy) metadata extraction (using
    Schemato).

.. _Docutils: http://docutils.sourceforge.net/
.. _reStructuredText: http://docutils.sourceforge.net/rst.html
.. _S5: http://meyerweb.com/eric/tools/s5/

Meta Information
----------------

**Me**: I've been using Python for 10 years. I use Python full-time, and have for the last 3 years.

**Startup**: I'm co-founder/CTO of Parse.ly_, a tech startup in the digital media space.

**E-mail me**: andrew@parsely.com

**Follow me on Twitter**: amontalenti_

**Connect on LinkedIn**: http://linkedin.com/in/andrewmontalenti

.. _Parse.ly: http://parsely.com
.. _amontalenti: http://twitter.com/amontalenti

Parse.ly
--------

What do we do?

How do we do it?

Crawler
-------

"A computer program that browses the World Wide Web in a methodical, automated
manner or in an orderly fashion."

Open source examples:

* Apache Nutch: built by Doug Cutting, creator of Lucene/Hadoop
* Heritrix: built by the Internet Archive

Web Data
--------

.. class:: incremental

    40 billion pages on the Web today (Google)

    Growing: size was "just" 15 billion in October 2010

    "Deep Web" means it's even bigger

Crawling, Spidering, Scraping
-----------------------------

These terms are almost synonymous, but sometimes have different meaning and connotations.

My take:

.. class:: incremental

    * Crawling: downloads and processes web content at one or more URLs

    * Spidering: walks links found in web content, either for a single domain or across the web

    * Scraping: uses knowledge of HTML pages to convert them into structured data


My first experience with crawlers
---------------------------------

.. class:: incremental

    Parse.ly Reader: personalized news reader built in mid 2009

    Crawled 500K web sources for content personalized to individual interests

    First crawler was really dumb: based on RSS/Atom detection and feed fetching

    Seeded from domains appearing on top aggregators like Google News

    Technology: multiprocessing, Postgres, Solr

My current experience
---------------------

.. class:: incremental

    Parse.ly shifted into web publisher analytics and APIs in 2010/2011

    Upgrades: Scrapy, MongoDB, Redis, Solr, Celery


Parse.ly Network Stats
----------------------

.. class:: incremental

    >200 top publishing domains (Quantcast top-10,000 sites)

    >3B pageviews/month across network

    >10M unique URLs in our index

    >1TB of hot production data running in memory

Parse.ly Crawl Infrastructure
-----------------------------

.. class:: incremental

    Have written 125 custom Scrapy crawlers with >10K of custom crawler code

    (Not proud of this fact; more on this later)

    Production environment in Rackspace Cloud; several worker nodes

    Implementation and QA runs in Scrapy Cloud

    Caching and retry strategy implemented atop Redis

    Eventual storage in MongoDB, Solr

    Implemented as Scrapy Components and Pipelines

Our Business
------------

We're not in the crawling business.

We're in the analytics and APIs business.

Our Strategy
------------

.. class:: incremental

    We aim to be the #1 technology partner for large-scale publishers.

    Crawling: means to an end.

    URLs => Structured Metadata.

    Metadata Soup: Schema.org, rNews, OpenGraph, hNews, HTML5, ...

Parse.ly Demo Time!
-------------------

Yay!

Reflections on Scaling Crawlers
-------------------------------

.. class:: incremental

    You don't want to write your own crawler infrastructure from scratch.

    TRUST ME.

    Lots of hidden problems --
    
    Abstractions: asynchronous network I/O (Twisted), data processing pipelines
    
    HTTP/web: retries, throttling, backoff, concurrency, cookie/form handling

    Infrastructure: crawling queues, health monitoring

Don't use Nutch, Heritrix
-------------------------

.. class:: incremental

    Didier and I tried to understand, and even customize, Nutch in the early days.

    We love Lucene/Solr, so we figured it'd be a good fit.

    But no -- it's a WORLD OF PAIN.

    (They are for building search engines and archives -- not structured metadata.)

Use Scrapy
----------

It's really Pythonic.

It's built on proven tools, like Twisted, w3lib, and lxml.

It's getting better and better.

Just trust me: use Scrapy.

Scrapy Overview
---------------

.. sourcecode:: sh

    $ git clone git://github.com/scrapy/dirbot.git
    $ cd dirbot
    $ mkvirtualenv dirbot
    $ pip install scrapy
    $ pip install ipython
    $ scrapy list
    dmoz
    $ scrapy crawl dmoz
    [scrapy] INFO: Scrapy 0.16.0 started (bot: dirbot)
    ...

Example Output
--------------

.. sourcecode:: sh

    [dmoz] DEBUG: Crawled (200) <GET http://dmoz.org/Comp.../Python/Resources/>
    [dmoz] DEBUG: Crawled (200) <GET http://dmoz.org/Comp.../Python/Books/>
    [dmoz] DEBUG: Scraped from <200 http://dmoz.org/Comp.../Python/Resources/>
                Website: name=[u'Top'] url=[u'/']
    [dmoz] DEBUG: Scraped from <200 http://dmoz.org/Comp.../Python/Resources/>
                Website: name=[u'Computers'] url=[u'/Computers/']
    [dmoz] DEBUG: Scraped from <200 http://dmoz.org/Comp.../Python/Resources/>
                Website: name=[u'Programming'] url=[u'/Computers/Programming/']
    ...
    [dmoz] DEBUG: Scraped from <200 http://dmoz.org/.../Python/Books/>
        Website: name=[u'Text Processing in Python'] url=[u'http://gnosis.cx/TPiP/']

    [dmoz] INFO: Spider closed (finished)

Links:

* http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/ 
* http://www.dmoz.org/Computers/Programming/Languages/Python/Books/

Spider Example
--------------

.. sourcecode:: python

    class DmozSpider(BaseSpider):
        name = "dmoz"
        allowed_domains = ["dmoz.org"]
        start_urls = [
            "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
            "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/",
        ]

        def parse(self, response):
            hxs = HtmlXPathSelector(response)
            sites = hxs.select('//ul/li')
            items = []
            for site in sites:
                item = Website()
                item['name'] = site.select('a/text()').extract()
                item['url'] = site.select('a/@href').extract()
                item['description'] = site.select('text()').extract()
                items.append(item)
            return items

Live Demos!
-----------

Examples: DailyCaller.com, ArsTechnica.com

* https://www.stypi.com/pixelmonkey/dailycaller.py
* https://www.stypi.com/pixelmonkey/arstechnica.py

Item
----

.. sourcecode:: python

    from scrapy.item import Item, Field

    class DbItem(Item):
        title = Field()
        link = Field()

DailyCaller: imperative style
-----------------------------

.. sourcecode:: python

    from scrapy.spider import BaseSpider
    from scrapy.selector import HtmlXPathSelector
    from dirbot.items import DbItem

    class DailycallerSpider(BaseSpider):
        name = "dailycaller.com"
        allowed_domains = ["dailycaller.com"]
        start_urls = ["http://dailycaller.com"]

        def parse(self, response):
            hxs = HtmlXPathSelector(response)
            item = DbItem()
            item["title"] = hxs.select("//h1/text()").extract()[0]
            item["link"] = hxs.select("//link[@rel='canonical']/@href").extract()[0]
            return item

ArsTechnica: declarative style
------------------------------

.. sourcecode:: python

    from scrapy.spider import BaseSpider
    from scrapy.selector import HtmlXPathSelector
    from dirbot.items import DbItem

    from scrapy.contrib.loader import XPathItemLoader
    from scrapy.contrib.loader.processor import TakeFirst

    class ArstechnicaSpider(BaseSpider):
        name = "arstechnica.com"
        allowed_domains = ["arstechnica.com"]
        start_urls = ["http://arstechnica.com"]

        def parse(self, response):
            loader = XPathItemLoader(item=DbItem(), response=response)
            loader.add_xpath("title", "//meta/[@property='og:title']/@content")
            loader.add_xpath("link", "//link[@rel='canonical']/@href")
            item = loader.load_item()
            item["title"] = loader.get_value(item["title"], TakeFirst(), unicode.title)
            item["link"] = loader.get_value(item["link"], TakeFirst())
            return item

Live Spider Shell
-----------------

.. sourcecode:: python

    >>> fetch("http://dailycaller.com/2012...-most-rallies/")
    [dailycaller.com] INFO: Spider opened
    [dailycaller.com] DEBUG: Crawled (200) <GET http://dailycaller.com/...ies/>
    [s] Available Scrapy objects:
    [s]   hxs        <HtmlXPathSelector xpath...>
    [s]   item       Website: name=None url=None
    [s]   request    <GET http://dailycaller.com/...es/>
    [s]   response   <200 http://dailycaller.com/...es/>
    [s]   settings   <CrawlerSettings module=<module 'dirbot.settings'>
    [s]   spider     <DailycallerSpider 'dailycaller.com' at 0x2484d90>
    [s] Useful shortcuts:
    [s]   shelp()           Shell help
    [s]   fetch(req_or_url) Fetch request (or URL) and update local objects
    [s]   view(response)    View response in a browser
    >>> hxs.select("//title/text()")

Scrapy Cloud Demo
-----------------

How we host, test, and QA our spiders across millions of pages.

Schemato Overview
-----------------

Domo arigato, Mr. Schemato!

Schemato Distilling
-------------------

.. sourcecode:: python

    from distillers import Distill, Distiller                                                                                                              
    class NewsDistiller(Distiller):
        title = Distill("s:headline", "og:title")
        image_url = Distill("s:associatedMedia.ImageObject/url", "og:image")
        pub_date = Distill("s:datePublished")
        author = Distill("s:author", "s:creator.Person/name")
        section = Distill("s:articleSection")
        description = Distill("s:description", "og:description")
        link = Distill("s:url", "og:url")
        id = Distill("s:identifier")   

Schemato Distilling in Action
-----------------------------

.. sourcecode:: python

    >>> from distillery import NewsDistiller
    >>> from schemato import Schemato
    >>> lnk = "http://www.cnn.com/2012/10/26/world/europe/italy-berlusconi-convicted/index.html"
    >>> cnn = Schemato(lnk)
    >>> distiller = NewsDistiller(cnn)    
    >>> distiller.distill()
    {'author': "Ben Wedeman",
    'id': None,
    'image_url': 'http://i2.cdn.turner.com/cnn/...-video-tease.jpg',
    'link': 'http://www.cnn.com/2012/10/26/world/europe/italy-berlusconi-convicted/index.html',
    'pub_date': '2012-10-26T14:36:35Z',
    'section': 'world',
    'title': 'Ex-Italian PM Berlusconi handed 4-year prison term for tax fraud',
    'site': 'CNN',
    'description': 'Flamboyant former Italian Prime Minister...'}

Schemato: Bridging Gaps Between Standards
-----------------------------------------

Facebook OpenGraph provided image_url and link.

Schema.org NewsArticle provided the rest.

.. sourcecode:: python

    >>> distiller.sources 
    {'author': 's:author',
    'id': None,
    'image_url': 'og:image',
    'link': 'og:url',
    'pub_date': 's:datePublished',
    'section': 's:articleSection',
    'title': 's:headline',
    'site': 'og:site_id',
    'description': 's:description'}

Data sets to get started
------------------------

Are you interested in tackling some of these web crawling problems on your own project?

If so, you may want some data to get started.

I currently sell a few news data sets that help with this:

* 30M news headlines and 500K web sources, 30gb of JSON data ($300)
* 15K news domains that are the most popular in US market ($100)

You could use either of these to build your own Google News, for example.

Interested? Find me after or tweet me: amontalenti_

Schemato: A Call to Action
--------------------------

The time is ripe for the semantic web.

Want to build the ultimate web metadata validator, distiller, and extractor?

Want to work on getting Schemato to run across millions of URLs?

Want your contributions open source on Github?

Find me at the sprints on Sunday!

Baby Turtles
------------

Use your powers wisely, and always remember...

.. image:: img/babyturtles.png
    :align: center

Magic Turtles!
--------------

It's turtles all the way down!

.. image:: img/magicturtle.jpg
    :align: center
