---
layout: post
title: Install Tomcat and Enable 80 port On Ubuntu
date: 2015-03-05 13:07
author: afeilulu
comments: true
categories: [program]
---
<h3>Step 1. Install Tomcat7</h3>
Update the package repository
<pre class="c">sudo apt-get update
sudo apt-get upgrade --show-upgrades</pre>
Install the authbind package
<pre class="c">sudo apt-get install authbind</pre>
This package allows non-root users to bind to ports below 1024. Install the tomcat7 package
<pre class="c">sudo apt-get install tomcat7</pre>
The tomcat7-docs, tomcat7-examples,and tomcat7-admin packages aren't required to deploy and maintain a web application. These packages can be skipped unless they are truly needed.

A new user "tomcat7" with primary group "tomcat7" will be created.

Enable authbind
Open a text editor like vim and load the default tomcat configuration file
<pre class="c">nano /etc/default/tomcat7</pre>
Remove the comment hash "#" in front of the authbind property and set the value to "yes"
<pre class="c">AUTHBIND=yes</pre>
Create authbind bind permission files for ports 80 and 443. Authbind will loop through the directory structure and try to find a byPort file referencing the port number attempting to be bound. Authorization will be granted if the user attempting to bind has execute permission.
<pre class="c">touch /etc/authbind/byport/80
touch /etc/authbind/byport/443
chmod 0755 /etc/authbind/byport/80
chmod 0755 /etc/authbind/byport/443
chown tomcat7:tomcat7 /etc/authbind/byport/80
chown tomcat7:tomcat7 /etc/authbind/byport/443</pre>
Change the Tomcat Port from 8080/8443 to 80/443
Open a text editor like vim and load the server configuration file
<pre class="c">nano /etc/tomcat7/server.xml</pre>
Find the connector for port 8080 to port 80 and the redirect port from 8443 to 443:
<pre class="c">&lt;Connector port="80" protocol="HTTP/1.1" 
connectiontimeout="20000" uriencoding="UTF-8" 
redirectport="443"&gt;
&lt;/Connector&gt;</pre>
Restart the Tomcat service
<pre class="c">sudo service tomcat7 restart</pre>
View the catalina.out log after restart and look for any errors. If you see permission denied errors, then you may have missed a step like:
Forgetting to uncomment the AuthBind setting and putting it to "yes"
Forgetting to restart the tomcat7 service as sudo

Now check connection with <em>netstat -ant</em>
<h3>Step 2. Deploy application</h3>
1. package your application and rename as ROOT.war

2. create logs directory under /usr/share/tomcat7
<pre class="c">cd /usr/share/tomcat7
sudo mkdir logs
sudo chmod 755 logs</pre>
3. create ROOT.xml file with the following content &lt;optional&gt;
<pre class="c">&lt;!-- The contents of this file will be loaded for each web application --&gt;


&lt;Context path="" docBase="/usr/share/tomcat7/ROOT.war" displayName="Tomcat_1" reload="false" 
        debug="0" privileged="true"&gt;

    &lt;!-- Default set of monitored resources --&gt;

    &lt;WatchedResource&gt;WEB-INF/web.xml&lt;/WatchedResource&gt;

&lt;/Context&gt;</pre>
docBase="/usr/share/tomcat7/ROOT.war" is where you uploaded war file.

4. Instead of creating ROOT.xml file, you can just upload your war file to the default webapps directory <strong>/var/lib/tomcat7/webapps/</strong>

5. restart tomcat
<pre class="c">sudo service tomcat7 restart</pre>
