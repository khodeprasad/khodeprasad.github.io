---
layout: post
title: Create Table in HBase using Java
tags: [Configuration, Connection, Create Table, Hadoop, HBase, Java, Java HBase Integration]
---
To create table into HBase by using Java we will use _**hbase-client.jar**_ which is available in maven repository. The following dependency can be added in our `pom.xml`

{% highlight java linenos %}
<dependency>
     <groupId>org.apache.hbase</groupId>
     <artifactId>hbase-client</artifactId>
     <version>1.1.0.1</version>
<dependency>
{% endhighlight %}

Once we have added the dependency we need to create `Configuration` object specifying `core-site.xml` and `hbase-site.xml` as resources.

{% highlight java linenos %}
Configuration config = HBaseConfiguration.create();
config.addResource(new Path("/etc/hbase/conf/hbase-site.xml"));
config.addResource(new Path("/etc/hadoop/conf/core-site.xml"));
{% endhighlight %}

Instead we can set `hbase.zookeeper.quorum` and `hbase.zookeeper.property.clientPort` values to Configuration object. These values can be found in `hbase-site.xml`

{% highlight java linenos %}
Configuration config = HBaseConfiguration.create();
config.set("hbase.zookeeper.quorum", "127.0.0.1");
config.set("hbase.zookeeper.property.clientPort", "2181");
{% endhighlight %}

Once we have Configuration object we can create Connection to HBase and from Connection object we obtain Admin object to perform create table operation.

{% highlight java linenos %}
Connection connection = ConnectionFactory.createConnection(config);
Admin admin = connection.getAdmin();
{% endhighlight %}

Once we have Admin object, we can create the table by using the following code. Here we are creating <strong><em>users</em></strong> table

{% highlight java linenos %}
String tableName = "users";

if (!admin.isTableAvailable(TableName.valueOf(tableName))) {
    HTableDescriptor hbaseTable = new HTableDescriptor(TableName.valueOf(tableName));
    hbaseTable.addFamily(new HColumnDescriptor("name"));
    hbaseTable.addFamily(new HColumnDescriptor("contact_info"));
    hbaseTable.addFamily(new HColumnDescriptor("personal_info"));
    admin.createTable(hbaseTable);
}
{% endhighlight %}

This will validate whether there exists a table with name "_users_", if not then we are creating a new table with column families `name`, `contact_info` and `personal_info`.

The complete program will look like below

{% highlight java linenos %}
package com.wordpress.khodeprasad;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.client.Admin;
import org.apache.hadoop.hbase.client.Connection;
import org.apache.hadoop.hbase.client.ConnectionFactory;

/**
* @author Prasad Khode
*
*/
public class CreateTable {

    public static void main(String[] args) {
        CreateTable object = new CreateTable();
        object.createTable();
    }

    public void createTable() {
        Configuration config = HBaseConfiguration.create();
        config.set("hbase.zookeeper.quorum", "127.0.0.1");
        config.set("hbase.zookeeper.property.clientPort", "2181");

        Connection connection = null;
        Admin admin = null;

        try {
            connection = ConnectionFactory.createConnection(config);
            admin = connection.getAdmin();

            String tableName = "users";

            if (!admin.isTableAvailable(TableName.valueOf(tableName))) {
                HTableDescriptor hbaseTable = new HTableDescriptor(TableName.valueOf(tableName));
                hbaseTable.addFamily(new HColumnDescriptor("name"));
                hbaseTable.addFamily(new HColumnDescriptor("contact_info"));
                hbaseTable.addFamily(new HColumnDescriptor("personal_info"));
                admin.createTable(hbaseTable);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (admin != null) {
                    admin.close();
                }

                if (connection != null && !connection.isClosed()) {
                    connection.close();
                }
            } catch (Exception e2) {
                e2.printStackTrace();
            }
        }
    }
}
{% endhighlight %}

For complete working code you can check <a href="https://github.com/khodeprasad/java-hbase/blob/master/src/main/java/com/khodeprasad/hbase/CreateTable.java" target="_blank">here</a>
