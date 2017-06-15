---
layout: post
title: How to use existing HBase table in Apache Phoenix
tags: [Apache HBase, Apache Phoenix, HBase, Phoenix, reuse, SELECT, Table, View]
---
__Apache Phoenix__ is an open source, relational database layer on top of noSQL store such as Apache HBase. Phoenix provides a JDBC driver that hides the intricacies of the noSQL store enabling users to create, delete, and alter SQL tables, views, indexes, and sequences; upsert and delete rows singly and in bulk; and query data through SQL.

To use an existing HBase table and data into Apache Phoenix we need to create a view in Phoenix pointing to the HBase table.

Lets say we have created a table `users_data` in our HBase table with few records in it. The following are commands to create and insert a record into HBase table.

{% highlight powershell linenos %}
create 'users_data', 'personal_info', 'contact_info'

put 'users_data', 'rowKey1','personal_info:firstName','Prasad'
put 'users_data', 'rowKey1','personal_info:lastName','Khode'
put 'users_data', 'rowKey1','personal_info:gender','male'
put 'users_data', 'rowKey1','contact_info:mail','xxxxxxxxxxx@gmail.com'
put 'users_data', 'rowKey1','contact_info:mobile','xxxxxxxxxx'
{% endhighlight %}

To start a terminal interface to execute SQL from the command line, execute the following from Phoenix bin directory:

{% highlight powershell linenos %}
phoenix-sqlline.py localhost
{% endhighlight %}

<img src="https://khodeprasad.files.wordpress.com/2016/07/phoenix_hbase_view_1.png" alt="phoenix_hbase_view_1" />

Now to use the same HBase table and query in Apache Phoenix, we create a view on top of HBase table like below:

{% highlight powershell linenos %}
CREATE VIEW "users_data" ( ROWKEY VARCHAR PRIMARY KEY, "personal_info"."firstName" VARCHAR, "personal_info"."lastName" VARCHAR, "personal_info"."gender" VARCHAR, "contact_info"."mail" VARCHAR, "contact_info"."mobile" VARCHAR ) ;
{% endhighlight %}

To check if the view created successfully use <b>!tables</b> command like below

{% highlight powershell linenos %}!tables{% endhighlight %}

<img src="https://khodeprasad.files.wordpress.com/2016/07/phoenix_hbase_view_2.png" alt="phoenix_hbase_view_2" />

Now we can perform select operations on the table

{% highlight powershell linenos %}SELECT * FROM "users_data";{% endhighlight %}

<img src="https://khodeprasad.files.wordpress.com/2016/07/phoenix_hbase_view_3.png" alt="phoenix_hbase_view_3" />

Now if we insert or update any records in HBase table the same will be reflected in our Phoenix view and we can query in SQL format.

<img src="https://khodeprasad.files.wordpress.com/2016/07/phoenix_hbase_view_4.png" alt="phoenix_hbase_view_4" />

<img src="https://khodeprasad.files.wordpress.com/2016/07/phoenix_hbase_view_5.png" alt="phoenix_hbase_view_5" />

If we want to remove the view then we can issue the following command

{% highlight powershell linenos %}DROP VIEW IF EXISTS "users_data";{% endhighlight %}

<img src="https://khodeprasad.files.wordpress.com/2016/07/phoenix_hbase_view_6.png" alt="phoenix_hbase_view_6" />

This will not delete the records from HBase table or will not delete HBase table

<span style="text-decoration:underline;"><strong>Update:</strong></span>

In case if you see the below error:

{% highlight powershell linenos %}
Error: ERROR 505 (42000): Table is read only. (state=42000,code=505)
org.apache.phoenix.schema.ReadOnlyTableException: ERROR 505 (42000): Table is read only.
{% endhighlight %}

then instead of creating a view, create table pointing to existing HBase table

{% highlight powershell linenos %}
CREATE TABLE "users_data" ( ROWKEY VARCHAR PRIMARY KEY, "personal_info"."firstName" VARCHAR, "personal_info"."lastName" VARCHAR, "personal_info"."gender" VARCHAR, "contact_info"."mail" VARCHAR, "contact_info"."mobile" VARCHAR ) ;
{% endhighlight %}

<strong>Note:</strong> In this case, if we drop the table using Phoenix drop command, then it will also drop the table from HBase
