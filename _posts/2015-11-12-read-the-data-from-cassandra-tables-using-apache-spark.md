---
layout: post
title: Read data from Cassandra tables using Apache Spark
tags: [Cassandra, Cassandra Spark Integration, Hadoop, Integration, READ, Spark]
---

<strong>Apache Cassandra</strong> is an open source distributed database management system designed to handle large amounts of data across many commodity servers, providing high availability with no single point of failure. Cassandra offers robust support for clusters spanning multiple datacenters, with asynchronous masterless replication allowing low latency operations for all clients.

<strong>Apache Spark</strong> is an open-source cluster computing framework originally developed in the AMPLab at UC Berkeley. In contrast to Hadoop's two-stage disk-based MapReduce paradigm, Spark's in-memory primitives provide performance up to 100 times faster for certain applications. By allowing user programs to load data into a cluster's memory and query it repeatedly, Spark is well-suited to machine learning algorithms.<!--more-->

Consider we have a table with name "table_user" with in our Cassandra database with the columns "user_first_name, user_last_name, user_email" then we will create a pojo class as following

{% highlight java linenos %}
public class UserBean implements Serializable {
    private static final long serialVersionUID = 3775871090088504659L;

    private String userFirstName;
    private String userLastName;
    private String userEmail;

    // getters & setters methods
}
{% endhighlight %}

To read the data from Cassandra tables using Apache Spark

1. Add Apache Spark & Cassandra dependencies in pom.xml
2. We need to configure SparkConf object with the Cassandra database details
3. By using SparkConf object create JavaSparkContext object
4. Use JavaSparkContext object to read the data from Cassandra table.

<span style="text-decoration:underline;">Step1</span>: For integrating Apache Spark & Cassandra database we will be using the following dependencies

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

<span style="text-decoration:underline;">Step4</span>: Once we have JavaSparkContext object we can read data from Cassandra table by providing the keyspace and table name using the following code snippet

{% highlight java linenos %}
String keySpaceName = "test_key_space";
String tableName = "table_user";

CassandraJavaRDD<CassandraRow> cassandraRDD = CassandraJavaUtil.javaFunctions(javaSparkContext).cassandraTable(keySpaceName, tableName);

JavaRDD<UserBean> userRDD = cassandraRDD.map(new Function<CassandraRow, UserBean>() {

    private static final long serialVersionUID = -165799649937652815L;

    @Override
    public UserBean call(CassandraRow row) throws Exception {
        UserBean userBean = new UserBean();
        userBean.setUserFirstName(row.getString("user_first_name"));
        userBean.setUserLastName(row.getString("user_last_name"));
        userBean.setUserEmail(row.getString("user_email"));
        return userBean;
    }
});
{% endhighlight %}

Now userRDD will have all the records from the table in the form of Spark RDD and we can perform any aggregate or spark operation on top of this filter

References: <br/>
https://en.wikipedia.org/wiki/Apache_Cassandra <br/>
https://en.wikipedia.org/wiki/Apache_Spark
