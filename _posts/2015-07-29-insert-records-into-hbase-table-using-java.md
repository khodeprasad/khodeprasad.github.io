---
layout: post
title: Insert records into HBase table using Java
tags: [Configuration, Insert, Integration, Java, Java HBase, Records, Table]
---

_**hbase-client.jar**_ will be used to get connected to HBase using Java and this is available in maven repository. The following dependency can be added in our `pom.xml`

{% highlight java linenos %}
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client<artifactId>
    <version>1.1.0.1</version>
<dependency>
{% endhighlight %}

Once we have added the dependency we need to create `Configuration` object specifying _**core-site.xml**_ and _**hbase-site.xml**_ as resources.

{% highlight java linenos %}
Configuration config = HBaseConfiguration.create();
config.addResource(new Path("/etc/hbase/conf/hbase-site.xml"));
config.addResource(new Path("/etc/hadoop/conf/core-site.xml"));
{% endhighlight %}

Instead we can set `hbase.zookeeper.quorum` and `hbase.zookeeper.property.clientPort` values to Configuration object. These values can be found in _**hbase-site.xml**_

{% highlight java linenos %}
Configuration config = HBaseConfiguration.create();
config.set("hbase.zookeeper.quorum", "127.0.0.1");
config.set("hbase.zookeeper.property.clientPort", "2181");
{% endhighlight %}

Once we have Configuration object we can create Connection to HBase and from `Connection` object we obtain `Table` object to perform insert operation.

{% highlight java linenos %}
String tableName = "users";
Connection connection = ConnectionFactory.createConnection(config);
Table table = connection.getTable(TableName.valueOf(tableName));
{% endhighlight %}

Now we need to use this table object to put the data into HBase using the following snippet

{% highlight java linenos %}
//    creating sample data that can be used to save into hbase table
String[][] people = {
    { "1", "Marcel", "Haddad", "marcel@xyz.com", "M", "26" },
    { "2", "Franklin", "Holtz", "franklin@xyz.com", "M", "24" },
    { "3", "Dwayne", "McKee", "dwayne@xyz.com", "M", "27" },
    { "4", "Rae", "Schroeder", "rae@xyz.com", "F", "31" },
    { "5", "Rosalie", "burton", "rosalie@xyz.com", "F", "25" },
    { "6", "Gabriela", "Ingram", "gabriela@xyz.com", "F", "24" } };

for (int i = 0; i < people.length; i++) {
    Put person = new Put(Bytes.toBytes(people[i][0]));
    person.addColumn(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
    person.addColumn(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
    person.addColumn(Bytes.toBytes("contact_info"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
    person.addColumn(Bytes.toBytes("personal_info"), Bytes.toBytes("gender"), Bytes.toBytes(people[i][4]));
    person.addColumn(Bytes.toBytes("personal_info"), Bytes.toBytes("age"), Bytes.toBytes(people[i][5]));
    table.put(person);
}
{% endhighlight %}

The complete program will look like below

{% highlight java linenos %}
package com.wordpress.khodeprasad;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.client.Connection;
import org.apache.hadoop.hbase.client.ConnectionFactory;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Table;
import org.apache.hadoop.hbase.util.Bytes;

/**
 * @author Prasad Khode
 *
 */
public class InsertIntoTable {

    public static void main(String[] args) {
        InsertIntoTable object = new InsertIntoTable();
        object.insertRecords();
    }

    public void insertRecords() {
         Configuration config = HBaseConfiguration.create();
         config.set("hbase.zookeeper.quorum", "127.0.0.1");
         config.set("hbase.zookeeper.property.clientPort", "2181");

         String tableName = "users";

         Connection connection = null;
         Table table = null;

         try {
            connection = ConnectionFactory.createConnection(config);
            table = connection.getTable(TableName.valueOf(tableName));

            //    creating sample data that can be used to save into hbase table
            String[][] people = {
                    { "1", "Marcel", "Haddad", "marcel@xyz.com", "M", "26" },
                    { "2", "Franklin", "Holtz", "franklin@xyz.com", "M", "24" },
                    { "3", "Dwayne", "McKee", "dwayne@xyz.com", "M", "27" },
                    { "4", "Rae", "Schroeder", "rae@xyz.com", "F", "31" },
                    { "5", "Rosalie", "burton", "rosalie@xyz.com", "F", "25" },
                    { "6", "Gabriela", "Ingram", "gabriela@xyz.com", "F", "24" } };

            for (int i = 0; i < people.length; i++) {
                Put person = new Put(Bytes.toBytes(people[i][0]));
                person.addColumn(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
                person.addColumn(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
                person.addColumn(Bytes.toBytes("contact_info"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
                person.addColumn(Bytes.toBytes("personal_info"), Bytes.toBytes("gender"), Bytes.toBytes(people[i][4]));
                person.addColumn(Bytes.toBytes("personal_info"), Bytes.toBytes("age"), Bytes.toBytes(people[i][5]));
                table.put(person);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (table != null) {
                    table.close();
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

To create tables in HBase programmatically using Java, refer my previous post <a href="https://khodeprasad.github.io/blog/create-table-in-hbase-using-java" target="_blank">here</a>

For complete working code you can check <a href="https://github.com/khodeprasad/java-hbase/blob/master/src/main/java/com/khodeprasad/hbase/InsertIntoTable.java" target="_blank">here</a>
