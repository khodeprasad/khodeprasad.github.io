---
layout: post
title: HBase Shell Commands
tags: [HBase, hbase shell, commands, create, describe, list, namespace, status, whoami]
---

HBase is free, open-source software from the Apache Foundation. It is a cross platform technology, so we can run it on Linux, Windows or OS/X machines and also can be hosted on Amazon Web Services and Microsoft Azure.

HBase is a NoSQL database which can run on a single machine, or cluster of servers. HBase provides data access in real-time. HBase tables can store billions of rows and millions of columns, unlike other big data technologies, which are batch-oriented. In HBase we have few key concepts like row key structure, column families, and regions.

To connection to HBase terminal:
{% highlight shell linenos %}
> hbase shell
{% endhighlight %}

then it will change the prompt to <em>hbase></em>;

<strong>General Commands:</strong>

1. To check the version of Hbase

{% highlight shell linenos %}
hbase> version
{% endhighlight %}

2. To check the current hbase user

{% highlight shell linenos %}
hbase> whoami
{% endhighlight %}

3. To show cluster status. Can be 'summary', 'simple', 'detailed', or 'replication'. The default is 'summary'.

{% highlight shell linenos %}
hbase> status
hbase> status 'simple'
hbase> status 'summary'
hbase> status 'detailed'
hbase> status 'replication'
hbase> status 'replication', 'source'
hbase> status 'replication', 'sink'
{% endhighlight %}

<strong>Namespace Commands:</strong>

1. To create namespace in HBase

{% highlight shell linenos %}
hbase> create_namespace 'namespace1'
{% endhighlight %}

2. To describe namespace in HBase

{% highlight shell linenos %}
hbase> describe_namespace 'namespace1'
{% endhighlight %}

3. To list all namespaces in HBase

{% highlight shell linenos %}
hbase> list_namespace
{% endhighlight %}

Optional regular expression parameter could be used to filter the output.

{% highlight shell linenos %}
hbase> list_namespace 'name.*'
{% endhighlight %}

4. To drop the name space in HBase. To drop the name space, make sure that namespace is empty.

{% highlight shell linenos %}
hbase> drop_namespace 'namespace1'
{% endhighlight %}

5. To list all tables that are members of the namespace in HBase

{% highlight shell linenos %}
hbase> list_namespace_tables 'namespace1'
{% endhighlight %}


References: <br/>
https://hbase.apache.org/ <br/>
https://en.wikipedia.org/wiki/Apache_HBase
