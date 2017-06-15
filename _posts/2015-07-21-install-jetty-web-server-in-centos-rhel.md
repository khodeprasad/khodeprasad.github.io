---
layout: post
title: Install Jetty web server in CentOS/RHEL
tags: [Configure, Deployment, Eclipse Foundation, Java, Java Servlet container, Jetty, Jetty server, jetty web server, Server, Web Applications, Web Server]
---

<p style="text-align:center;"><img src="http://www.eclipse.org/jetty/images/jetty-logo-80x22.png" alt="jetty" /></p>

Jetty is a Java HTTP (Web) server and Java Servlet container. While Web Servers are usually associated with serving documents to people, Jetty is now often used for machine to machine communications, usually within larger software frameworks. Jetty is developed as a free and open source project as part of the Eclipse Foundation. The web server is used in products such as Apache ActiveMQ, Alfresco, Apache Geronimo, Apache Maven, Apache Spark, Google App Engine, Eclipse, FUSE, iDempiere, Twitter's Streaming API and Zimbra. Jetty is also the server in open source projects such as Lift, Eucalyptus, Red5, Hadoop and I2P. Jetty supports the latest Java Servlet API (with JSP support) as well as protocols HTTP/2 and WebSocket.

This guide will help you to setup jetty on CentOS / RHEL

* Install Java JDK as it is required to install and run Jetty
{% highlight powershell linenos %}sudo yum install java-1.7.0-openjdk{% endhighlight %}

* Download jetty from <a target="_blank" href="https://www.eclipse.org/jetty/download.html" alt="jetty download">https://www.eclipse.org/jetty/download.html</a>. As we have JDK 1.7 I will download Jetty server 9.2.12 as it is compatible to the JDK 1.7
{% highlight powershell linenos %}wget http://download.eclipse.org/jetty/9.2.12.v20150709/dist/jetty-distribution-9.2.12.v20150709.tar.gz{% endhighlight %}

* Extract the downloaded `tar.gz` file to `/opt` directory
{% highlight powershell linenos %}tar zxvf jetty-distribution-9.2.12.v20150709.tar.gz -C /opt/{% endhighlight %}

* Rename it to jetty
{% highlight powershell linenos %}mv /opt/jetty-distribution-9.2.12.v20150709/ /opt/jetty{% endhighlight %}

* Create a user called jetty to run jetty web server on system startup
{% highlight powershell linenos %}useradd -m jetty{% endhighlight %}

* Change ownership of extracted jetty directory
{% highlight powershell linenos %}chown -R jetty:jetty /opt/jetty/{% endhighlight %}

* Copy or Symlink `jetty.sh` to `/etc/init.d` directory to create a start up script file for jetty web server
{% highlight powershell linenos %}ln -s /opt/jetty/bin/jetty.sh /etc/init.d/jetty{% endhighlight %}

* Add script
{% highlight powershell linenos %}chkconfig --add jetty{% endhighlight %}

* Auto start at 3,4 and 5 levels
{% highlight powershell linenos %}chkconfig --level 345 jetty on{% endhighlight %}

* Add the following information in `/etc/default/jetty`, replace port and listening address with your value
{% highlight powershell linenos %}
vi /etc/default/jetty

 JETTY_HOME=/opt/jetty
 JETTY_USER=jetty
 JETTY_HOST=0.0.0.0
 JETTY_LOGS=/opt/jetty/logs/
 JETTY_ARGS=jetty.port=8080
{% endhighlight %}

* Now start jetty service
{% highlight powershell linenos %}service jetty start{% endhighlight %}

* Jetty can be accessed using web browser at `http://your-ip-address:8080`

__References:__<br/>
[https://en.wikipedia.org/wiki/Jetty_(web_server)](https://en.wikipedia.org/wiki/Jetty_(web_server))<br/>
[http://www.eclipse.org/jetty/](http://www.eclipse.org/jetty/)
