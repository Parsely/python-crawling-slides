.. include:: <s5defs.txt>

=====================================
Web Crawling and Metadata with Python
=====================================

:Author:  Andrew Montalenti
:Date:    $Date: 2012-10-26 09:00:00 -0500 (Tues, 23 Oct) $

.. This document is copyright Andrew Montalenti and Parsely, Inc.

.. container:: handout

    **How this was made**

    This document was created using Docutils_/reStructuredText_ and S5_.

    It is the introductory NLP course given by Parsely, Inc. to
    the newest generation of Python hackers.

    Simplicity begets elegance.

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

Outline
-------

* brief introduction to scrapy
* some scrapy examples
* overview of scrapy cloud
* scrapy toolbox: w3lib, scrapely, slybot
* how crawling and metadata relate, and what it means to you
* some shortcuts to get started: datasets for sale, diffbot API

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
