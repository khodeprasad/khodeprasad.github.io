---
layout: post
title: Installing Apache Solr
tags: [Configuring, Indexing, Installing, Search, Solr, Hadoop, BigData]
---

<strong>Apache Solr</strong> is an opensource search platform powered by Apache Lucene written in Java. Solr is standalone search server with REST-like API. We index documents in it via JSON, XML, CSV or binary over HTTP. We query it via HTTP GET and receive JSON, XML, CSV or binay results.

The following are the features of Apache Solr

1. Advanced Full-Text Search Capabilities
2. Optimized for High Volumn Traffic
3. Standards Based Open Interfaces - XML, JSON and HTTP
4. Comprehensive Administration Interfaces
5. Easy Monitoring
6. Highly Scalable and Fault Tolerant
7. Flexible and Adaptable with easy configuration
8. Near Real-Time Indexing
9. Extensible Plugin Architecture

<strong>Installing Apache Solr:</strong>

As of this writing, the latest & stable release of Apache Solr is 6.0.0. Download Apache Solr from http://lucene.apache.org/solr/mirrors-solr-latest-redir.html to install into your system/server. Once its downloaded, extract the file into some location and cd into that directory.

<img src="https://khodeprasad.files.wordpress.com/2016/05/solr-csv-screenshot-1.png" alt="Solr-CSV-screenshot-1" />

The <em>bin</em> folder will have the scripts to start and stop the server.

The <em>example</em> folder will have few example files and the same example files we will be using to demonstrate how Solr indexes the data.

The <em>server</em> folder contains the <em>logs</em> folder where all the Solr logs are written. It will be very helpful to check the logs for any error during indexing.

The <em>solr</em> folder under <em>server</em> holds different collections or cores which we are going to create. The configuration and data for each of the collection or core are stored in the respective collection or core folder.

Before we start the solr instance we must validate the <strong>JAVA_HOME</strong> is set on the machine. Apache Solr comes with an inbuilt Jetty server, we can start server using the command line script. Go to the bin directory from the command prompt and issue the following command

{% highlight java linenos %}
solr start
{% endhighlight %}

This will start the Solr server under the default port 8983. In case if we want to run Solr on different port then we can specify the port using the following command.

{% highlight java linenos %}
solr start -p &lt;port_number&gt;
{% endhighlight %}

To validate whether our Solr instance is running or not, open the following url in browser

http://localhost:8983/solr/

<img src="https://khodeprasad.files.wordpress.com/2016/05/solr-csv-screenshot-2.png" alt="Solr-CSV-screenshot-2" />
