---
layout: post
title: "ElasticSearch Postman Queries"
date: 2014-07-26 11:48:33 -0700
comments: true
categories: ElasticSearch, Postman
---
I was experimenting with getting [ElasticSearch](http://www.elasticsearch.org/) up and running. In doing so I was using 
the Chrome [Postman](http://www.getpostman.com/) extension to make requests against my local instance of ElasticSearch 
and experiment with the API.

Here are the postman request collections that I used. This first one
[Localhost Specific Request Collection](/resources/postman/ElasticSearchLocalhost.json.postman_collection) as the title 
states is specific to localhost and to port 9200. However I made another collection with the same requests that uses a 
[Postman environment](http://www.getpostman.com/docs/environments) to allow hitting an ElasticSearch cluster not on 
localhost. This [Request Collection](/resources/postman/ElasticSearch.json.postman_collection) allows you to setup a 
Postman environment with a host and port property that will change where the requests go. For more information about 
Postman environments see the [documentation](http://www.getpostman.com/docs/environments).

If you want more information on setting up ElasticSearch check out their
[Getting Started Guide](http://www.elasticsearch.org/guide/en/elasticsearch/guide/current/intro.html). Red Badger has 
a good blog post on
[Getting Started with ElasticSearch](http://red-badger.com/blog/2013/11/08/getting-started-with-elasticsearch/) if you 
want more instruction on getting up and running.
