---
layout: post
title: Installing Scala in RHEL / Cent OS
tags: [Configuring, Installing, Java, Scala, linux, RHEL, Cent OS]
---
To install Scala, it requires the Java run time version 1.8 or later. Once we have Java installed and configured, we can download the Scala distribution in RHEL or Cent OS using this command

{% highlight java linenos %}
wget http://www.scala-lang.org/files/archive/scala-2.12.1.tgz
{% endhighlight %}

Once the download is done, we will extract the distribution at the given location <strong>/usr/lib</strong>

{% highlight java linenos %}
sudo tar -xf scala-2.12.1.tgz -C /usr/lib
{% endhighlight %}

Lets create symbolic link to the scala directory

{% highlight java linenos %}
sudo ln -s /usr/lib/scala-2.12.1 /usr/lib/scala
{% endhighlight %}

Now we will add the scala bin directory to <strong>PATH</strong>

{% highlight java linenos %}
export PATH=$PATH:/usr/lib/scala/bin
{% endhighlight %}

Thats all we have to do. Now we can check our scala installation using the command

{% highlight java linenos %}
scala -version
{% endhighlight %}

It should print the following in terminal

<div style="padding:12px;background-color:#eff0f1;line-height:1.4;">
Scala code runner version 2.12.1 -- Copyright 2002-2016, LAMP/EPFL and Lightbend, Inc.
</div>

<br/>
