---
layout: post
title: Read records from HBase table using Java
tags: [CRUD, HBase, Java HBase, Java HBase Integration, Read Records, SELECT]
---
_**hbase-client.jar**_ will be used to get connected to HBase using Java and this is available in maven repository. The following dependency can be added in our `pom.xml`

{% highlight java linenos %}
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client<artifactId>
    <version>1.1.0.1</version>
<dependency>
{% endhighlight %}

Once we have added the dependency we need to create Configuration object specifying `core-site.xml` and `hbase-site.xml` as resources.

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

Once we have Configuration object we can create Connection to HBase and from Connection object we obtain Table object to perform read records from HBase table.

{% highlight java linenos %}
String tableName = "peoples";
Connection connection = ConnectionFactory.createConnection(config);
Table table = connection.getTable(TableName.valueOf(tableName));
{% endhighlight %}

Now we will create Scan object to read all the records from HBase table

{% highlight java linenos %}
Scan scan = new Scan();
scan.addColumn(Bytes.toBytes("name"), Bytes.toBytes("first"));
scan.addColumn(Bytes.toBytes("name"), Bytes.toBytes("last"));
scan.addColumn(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"));
scan.addColumn(Bytes.toBytes("personalinfo"), Bytes.toBytes("gender"));
scan.addColumn(Bytes.toBytes("personalinfo"), Bytes.toBytes("age"));
{% endhighlight %}

Once we have our Scan object we execute the scanner and get the ResultScanner which will be a ResultSet in our JDBC applications and from ResultScanner object we get our column values for each record like below

{% highlight java linenos %}
resultScanner = table.getScanner(scan);

for (Result result = resultScanner.next(); result != null; result = resultScanner.next()) {
	byte[] firstNameValue = result.getValue(Bytes.toBytes("name"), Bytes.toBytes("first"));
	byte[] lastNameValue = result.getValue(Bytes.toBytes("name"), Bytes.toBytes("last"));
	byte[] emailValue = result.getValue(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"));
	byte[] genderValue = result.getValue(Bytes.toBytes("personalinfo"), Bytes.toBytes("gender"));
	byte[] ageValue = result.getValue(Bytes.toBytes("personalinfo"), Bytes.toBytes("age"));

	String firstName = Bytes.toString(firstNameValue);
	String lastName = Bytes.toString(lastNameValue);
	String email = Bytes.toString(emailValue);
	String gender = Bytes.toString(genderValue);
	String age = Bytes.toString(ageValue);

	System.out.println("First Name : " + firstName + " --- Last Name : " + lastName + " --- Email : " + email + " --- Gender : " + gender + " --- Age : " + age);
}
{% endhighlight %}

The complete program will look like below

{% highlight java linenos %}

package com.khodeprasad.hbase;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.client.Connection;
import org.apache.hadoop.hbase.client.ConnectionFactory;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.Table;
import org.apache.hadoop.hbase.util.Bytes;

import com.khodeprasad.hbase.util.HBaseConfigUtil;

/**
 * @author Prasad Khode
 *
 */
public class ScanTable {

	public static void main(String[] args) {
		Configuration config = HBaseConfigUtil.getHBaseConfiguration();

		Connection connection = null;
		Table table = null;
		ResultScanner resultScanner = null;

		try {
			connection = ConnectionFactory.createConnection(config);
			table = connection.getTable(TableName.valueOf("peoples"));

			Scan scan = new Scan();
			scan.addColumn(Bytes.toBytes("name"), Bytes.toBytes("first"));
			scan.addColumn(Bytes.toBytes("name"), Bytes.toBytes("last"));
			scan.addColumn(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"));
			scan.addColumn(Bytes.toBytes("personalinfo"), Bytes.toBytes("gender"));
			scan.addColumn(Bytes.toBytes("personalinfo"), Bytes.toBytes("age"));

			resultScanner = table.getScanner(scan);

			for (Result result = resultScanner.next(); result != null; result = resultScanner.next()) {
				byte[] firstNameValue = result.getValue(Bytes.toBytes("name"), Bytes.toBytes("first"));
				byte[] lastNameValue = result.getValue(Bytes.toBytes("name"), Bytes.toBytes("last"));
				byte[] emailValue = result.getValue(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"));
				byte[] genderValue = result.getValue(Bytes.toBytes("personalinfo"), Bytes.toBytes("gender"));
				byte[] ageValue = result.getValue(Bytes.toBytes("personalinfo"), Bytes.toBytes("age"));

				String firstName = Bytes.toString(firstNameValue);
				String lastName = Bytes.toString(lastNameValue);
				String email = Bytes.toString(emailValue);
				String gender = Bytes.toString(genderValue);
				String age = Bytes.toString(ageValue);

				System.out.println("First Name : " + firstName + " --- Last Name : " + lastName + " --- Email : " + email + " --- Gender : " + gender + " --- Age : " + age);
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			try {
				if (resultScanner != null) {
					resultScanner.close();
				}

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

To create tables in HBase programmatically using Java, refer my previous post <a href="https://khodeprasad.github.io/blog/create-table-in-hbase-using-java/" target="_blank">here</a>

To insert records in HBase table using Java, refer my previous post <a href="https://khodeprasad..github.io/blog/insert-records-into-hbase-table-using-java/" target="_blank">here</a>

<p style="text-align:center;font-size:1.5em;">For complete working code you can check <a href="https://github.com/khodeprasad/java-hbase/blob/master/src/main/java/com/khodeprasad/hbase/ScanTable.java" target="_blank">here</a></p>