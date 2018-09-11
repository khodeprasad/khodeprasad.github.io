---
layout: post
title: Configuring Apache Phoenix in CDH 5.x using Cloudera Manager
tags: [Apache Phoenix, Hadoop, HBase, JDBC, SQL Layer, Wrapper]
---

<strong>Apache Phoenix</strong> is an open source, relational database layer on top of noSQL store such as Apache HBase. Phoenix provides a JDBC driver that hides the intricacies of the noSQL store enabling users to create, delete, and alter SQL tables, views, indexes, and sequences; upsert and delete rows singly and in bulk; and query data through SQL.

<h3><span>Installation:</span></h3>
Following are the steps that need to be followed to configure Apache Phoenix in Cloudera Distribution for Hadoop (CDH)
<ol>
	<li>Login to Cloudera Manager, click on Hosts, then Parcels.</li>
	<li>Select Edit Settings.</li>
	<li>Click the + sign next to an existing Remote Parcel Repository URL, and add the URL: <a href="http://archive.cloudera.com/cloudera-labs/phoenix/parcels/latest/" target="_blank">http://archive.cloudera.com/cloudera-labs/phoenix/parcels/latest/</a> Click Save Changes.</li>
	<li>Select Hosts, then Parcels.</li>
	<li>In the list of Parcel Names, CLABS_PHOENIX is now available. Select it and choose Download.</li>
	<li>The first cluster is selected by default. To choose a different cluster for distribution, select it. Find CLABS_PHOENIX in the list, and click Distribute.</li>
	<li>If you to use secondary indexing, add the following to the hbase-site.xml advanced configuration snippet. Go to the HBase service, click Configuration, and choose/search for <span style="text-decoration:underline;">HBase Service Advanced Configuration Snippet (Safety Valve) for hbase-site.xml</span>. Paste in the following XML, then save the changes.</li>
{% highlight java linenos %}
<property>
    <name>hbase.regionserver.wal.codec</name>
    <value>org.apache.hadoop.hbase.regionserver.wal.IndexedWALEditCodec</value>
</property>
{% endhighlight %}

<li>. Restart the HBase service.</li>
</ol>

<h3><span>Using Apache Phoenix Utilities</span></h3>
Several command-line utilities for Apache Phoenix are installed into /usr/bin.

<span style="text-decoration:underline;">Prerequisites</span>
Before using the Phoenix utilities, set the JAVA_HOME environment variable in your terminal session, and ensure that the java executable is in your path. Adjust the following commands to your operating system's configuration.

{% highlight shell linenos %}
$ export JAVA_HOME=/usr/java/jdk1.7.0_67-cloudera
$ export PATH=$PATH:$JAVA_HOME/bin
{% endhighlight %}

<strong>phoenix-sqlline.py</strong>
A command-line interface to execute SQL from the command line. It takes a single argument, which is the ZooKeeper quorum of the corresponding HBase cluster. For example:

{% highlight shell linenos %}
$ /usr/bin/phoenix-sqlline.py zookeeper01.test.com:2181
{% endhighlight %}

<strong>phoenix-psql.py</strong>
A command-line interface to load CSV data or execute SQL scripts. It takes two arguments, the ZooKeeper quorum and the CSV or SQL file to process. For example:

{% highlight shell linenos %}
$ /usr/bin/phoenix-psql.py zookeeper01.test.com:2181 create_stmts.sql data.csv
$ /usr/bin/phoenix-psql.py zookeeper01.test.com:2181 create_stmts.sql query.sql
{% endhighlight %}

<strong>phoenix-performance.py</strong>
A command-line interface to create a given number of rows and run timed queries against the data. It takes two arguments, the ZooKeeper quorum and the number of rows to create. For example:

{% highlight shell linenos %}
$ /usr/bin/phoenix-psql.py zookeeper01.test.com:2181 100000
{% endhighlight %}

References: <br/>
https://en.wikipedia.org/wiki/Apache_Phoenix <br/>
https://phoenix.apache.org/
