---
layout: post
title: Create MySQL Events / Schedulers
tags: [Cron, Events, Mysql, Schedulers]
---
MySQL Event is, performing or executing some operation based on the specified or scheduled time. MySQL Events have been added from version 5.1.6 MySQL event scheduler is a process that runs in background and looks for events to execute. Before we create or schedule an event in MySQL, we need to first verify whether its enabled or not Issue the following command to turn on the scheduler<!--more-->

{% highlight sql linenos %}
SET GLOBAL event_scheduler = ON;
{% endhighlight %}

To disable the scheduler run

{% highlight sql linenos %}
SET GLOBAL event_scheduler = OFF;
{% endhighlight %}

To check whether scheduler is enabled or not, use the following command

{% highlight sql linenos %}
select @@GLOBAL.event_scheduler;
{% endhighlight %}

Use the following command to list all the events that are created

{% highlight sql linenos %}
SHOW EVENTS;
{% endhighlight %}

By using events, we will insert records into some table lets call it as test_table, for each day at a specific time. Our test_table will look something like below

{% highlight sql linenos %}
CREATE TABLE test.test_table (
  id INT(11) NOT NULL AUTO_INCREMENT,
  created_date DATETIME DEFAULT NULL,
  PRIMARY KEY (id)
) ENGINE=INNODB DEFAULT CHARSET=utf8;
{% endhighlight %}

The following will be used for creating an event which will insert a record into test_table for every 1 minute

{% highlight sql linenos %}
DELIMITER $$

CREATE EVENT test.first_event
ON SCHEDULE EVERY 1 MINUTE
ON COMPLETION NOT PRESERVE ENABLE
DO
BEGIN
    INSERT INTO test.test_table(created_date) VALUES (CURRENT_TIMESTAMP);
END $$

DELIMITER ;
{% endhighlight %}

Once we have created our first_event, it will insert a new record for each minute into test_table. If we want to alter the existing event to run for each 1 hour then

{% highlight sql linenos %}
DELIMITER $$

ALTER EVENT test.first_event
ON SCHEDULE EVERY 1 HOUR $$

DELIMITER ;
{% endhighlight %}

The following can be used drop the events from the database

{% highlight sql linenos %}
DROP EVENT IF EXISTS test.first_event;
{% endhighlight %}

Reference:<br/>
[https://dev.mysql.com/doc/refman/5.1/en/events-overview.html](https://dev.mysql.com/doc/refman/5.1/en/events-overview.html)