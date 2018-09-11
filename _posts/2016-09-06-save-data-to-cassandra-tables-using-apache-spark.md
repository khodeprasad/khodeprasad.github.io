---
layout: post
title: Save data to Cassandra tables using Apache Spark
tags: [Cassandra, Apache Cassandra, Spark, Apache Spark, Big Data, BigData, Hadoop, Java, push, rdd, save, save into table, spark java, Table]
---

<strong>Apache Cassandra</strong> is an open source distributed database management system designed to handle large amounts of data across many commodity servers, providing high availability with no single point of failure. Cassandra offers robust support for clusters spanning multiple datacenters, with asynchronous masterless replication allowing low latency operations for all clients.

<strong>Apache Spark</strong> is an open-source cluster computing framework originally developed in the AMPLab at UC Berkeley. In contrast to Hadoop's two-stage disk-based MapReduce paradigm, Spark's in-memory primitives provide performance up to 100 times faster for certain applications. By allowing user programs to load data into a cluster's memory and query it repeatedly, Spark is well-suited to machine learning algorithms.<!--more-->

Consider we have a table with name "table_user" with in our Cassandra database with the columns "user_first_name, user_last_name, user_email" then we will create a pojo class as following

{% highlight java linenos %}
public class UserBean implements Serializable {
    private static final long serialVersionUID = 3775871090088504659L;

    private int id;

    private String userFirstName;
    private String userLastName;
    private String userEmail;

    public UserBean() {
    }

    public UserBean(int id, String userFirstName, String userLastName, String userEmail) {
        this.id = id;
        this.userFirstName = userFirstName;
        this.userLastName = userLastName;
        this.userEmail = userEmail;
    }

    // getters &amp;amp; setters methods
}
{% endhighlight %}

To save data to Cassandra tables using Apache Spark

1. Add Apache Spark &amp; Cassandra dependencies in pom.xml
2. We need to configure SparkConf object with the Cassandra database details
3. By using SparkConf object create JavaSparkContext object
4. Create RDD which holds the data that we want to save into Cassandra table.
5. Save the data from Spark RDD to Cassandra table.

<span style="text-decoration:underline;">Step1</span>: For integrating Apache Spark &amp; Cassandra database we will be using the following dependencies

{% highlight java linenos %}
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-core_2.10</artifactId>
    <version>1.2.1</version>
</dependency>
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-streaming_2.10</artifactId>
    <version>1.2.1</version>
</dependency>
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-sql_2.10</artifactId>
    <version>1.2.1</version>
</dependency>

<dependency>
    <groupId>com.datastax.spark</groupId>
    <artifactId>spark-cassandra-connector_2.10</artifactId>
    <version>1.2.1</version>
</dependency>
<dependency>
    <groupId>com.datastax.spark</groupId>
    <artifactId>spark-cassandra-connector-java_2.10</artifactId>
    <version>1.2.1</version>
</dependency>
{% endhighlight %}

<span style="text-decoration:underline;">Step2</span>: Configure SparkConf object with the Cassandra database details

{% highlight java linenos %}
SparkConf sparkConf = new SparkConf();
sparkConf.setAppName("Spark-Cassandra Integration");
sparkConf.setMaster("local[4]");
sparkConf.set("spark.cassandra.connection.host", "127.0.0.1");
sparkConf.set("spark.cassandra.connection.native.port", "9042");
sparkConf.set("spark.cassandra.connection.rpc.port", "9160");
sparkConf.set("spark.cassandra.connection.timeout_ms", "5000");
sparkConf.set("spark.cassandra.read.timeout_ms", "200000");
sparkConf.set("spark.cassandra.auth.username", "test_user");
sparkConf.set("spark.cassandra.auth.password", "test_password");
{% endhighlight %}

<span style="text-decoration:underline;">Step3</span>: Create JavaSparkContext object using SparkConf object

{% highlight java linenos %}
JavaSparkContext javaSparkContext = new JavaSparkContext(sparkConf);
{% endhighlight %}

<span style="text-decoration:underline;">Step4</span>: Once we have JavaSparkContext object we create RDD which is holding the data that we want to save into Cassandra table.

{% highlight java linenos %}

String keySpaceName = "test_key_space";
String tableName = "table_user";

List<UserBean> list = new ArrayList<>();
list.add(new UserBean(1, "userFirstName1", "userLastName1", "xxx@xxx.com"));
list.add(new UserBean(2, "userFirstName2", "userLastName2", "xxx@xxx.com"));
list.add(new UserBean(3, "userFirstName3", "userLastName3", "xxx@xxx.com"));

JavaRDD<UserBean> userRDD = javaSparkContext.parallelize(list);
{% endhighlight %}

<span style="text-decoration:underline;">Step5</span>: Save the data from Spark RDD to Cassandra table using the following.

{% highlight java linenos %}
CassandraJavaUtil.javaFunctions(userRDD).writerBuilder(keySpaceName, tableName, CassandraJavaUtil.mapToRow(UserBean.class)).saveToCassandra();
{% endhighlight %}


This will save all the records from Spark RDD into the Cassandra table. We can perform [code]cqlsh[/code] query or we can <a href="https://khodeprasad.wordpress.com/2015/11/12/read-the-data-from-cassandra-tables-using-apache-spark/">pull the data in Spark RDD</a> and perform any other operations

References:

https://en.wikipedia.org/wiki/Apache_Cassandra <br/>
https://en.wikipedia.org/wiki/Apache_Spark
