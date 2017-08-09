---
layout: post
title: Install Redmine on Ubuntu11.04
date: 2011-08-02 15:04
author: afeilulu
comments: true
categories: [program]
---
This file is based on
<a href="http://www.x2on.de/2011/04/23/tutorial-redmine-with-git-and-gitosis-on-ubuntu-11-04/">Tutorial: Redmine with Git and Gitosis on Ubuntu 11.04</a>
Step 1.Install Git

1.Install the dependent package

<span class="Apple-style-span" style="font-family:monospace;">    $sudo aptitude build-dep git-core</span>

2.Download the latest Git release from http://git-scm.com

<span class="Apple-style-span" style="font-family:monospace;">    $ wget http://kernel.org/pub/software/scm/git/git-1.7.6.tar.gz</span>
<div>    3.Extract the package and running the following<code>
</code>
<div><code>    $ tar xvzf git-1.7.6.tar.gz</code><code>
$ cd git-1.7.6
</code><code>    $ ./configure</code>
<code>    $ make</code>
<code>    $ sudo make install</code></div>
</div>
<div>

    4.Check the version

<span class="Apple-style-span" style="font-family:monospace;">    $git --version</span>

</div>
Step 2.Install Apache2
<div><code>    $apt-get install apache2</code></div>
Step 3.Install Mysql
<div><code>    $apt-get install mysql-server</code>
it will set your root password of mysql.
<code>    $apt-get install libmysqlclient-dev</code></div>
Step 4.Configure database setting
<div><code>    $mysql -u root -p&lt;
mysql&gt;CREATE DATABASE redmine CHARACTER SET utf8;
mysql&gt;CREATE USER 'redmine'@'localhost' IDENTIFIED BY 'redmine_password';
mysql&gt;GRANT ALL privileges ON redmine.* TO 'redmine'@'localhost';
mysql&gt;exit;edit configration file
$cp redmine/config/database.yml.example redmine/config/database.yml
$nano redmine/config/database.yml
production:
adapter: mysql
database: redmine
host: localhost
username: redmine
password: redmine_password
encoding: utf8
</code></div>
Step 5.Install Redmine
<div>    Get 1.1.3 version from github
<code>    $cd /var/www
$git clone git://github.com/edavis10/redmine.git
$cd redmine
$git checkout -b 1.1.3 1.1.3Install Ruby
$apt-get install ruby
$apt-get install ruby1.9.1-dev
$apt-get install libcurl4-openssl-dev
$apt-get install libssl-dev
$apt-get install apache2-prefork-dev
$apt-get install libapr1-dev
$apt-get install libaprutil1-dev
$apt-get install librmagick-ruby1.8 (if you want to use Gant-Charts)</code></div>
<div>Install Rubygem Ubuntu 11.04 has an older version.
<code>
$wget http://production.cf.rubygems.org/rubygems/rubygems-1.7.2.tgz
$tar xvfz rubygems-1.7.2.tgz
$cd rubygems-1.7.2
$mv /usr/bin/gem /usr/bin/gem-old
$ruby setup.rb
$ln -s /usr/bin/gem1.8 /usr/bin/gemInstall Rails and Rack
$gem install -v=2.3.11 rails
</code>
There is an incompatibility between Rails 2.3.8 and recent versions of RubyGems. Upgrade to the latest 2.3 version (2.3.11 as of today)
Setting RAILS_GEM_VERSION='2.3.11' in /var/www/redmine/config/environment.rb$gem install -v=1.1.0 rack
<code>
$gem install mysql (install mysql driver)
$gem install -v=0.4.2 i18nConfigure redmine
$cd /var/www/redmine
$chown -R www-data:www-data files log tmp public/plugin_assets
$chmod -R 755 files log tmp public/plugin_assets
$chmod -R 777 public/plugin_assets
$rake generate_session_store
$RAILS_ENV=production rake db:migrate
$RAILS_ENV=production rake redmine:load_default_dataStart redmine
$ruby script/server webrick -e production
</code>
Test in browser http://127.0.0.1:3000</div>
Step 6.Apache integration
<div>Install passenger
<code>
$gem install passenger
$passenger-install-apache2-module
</code>
it will guide you to install missing package.
And after the module was successfully installed.
it will show you the following.
<code>
--------------------------------------------
The Apache 2 module was successfully installed.Please edit your Apache configuration file, and add these lines:LoadModule passenger_module /usr/lib/ruby/gems/1.8/gems/passenger-3.0.7/ext/apache2/mod_passenger.so
PassengerRoot /usr/lib/ruby/gems/1.8/gems/passenger-3.0.7
PassengerRuby /usr/bin/ruby1.8After you restart Apache, you are ready to deploy any number of Ruby on Rails
applications on Apache, without any further Ruby on Rails-specific
configuration!
</code>let's do what it said.
<code>
$echo "LoadModule passenger_module /usr/lib/ruby/gems/1.8/gems/passenger-3.0.7/ext/apache2/mod_passenger.so" &gt; /etc/apache2/mods-available/passenger.load
$ln -s /etc/apache2/mods-available/passenger.load /etc/apache2/mods-enabled/passenger.load
$nano /etc/apache2/mods-available/passenger.conf
</code>
<div>adding the following to passenger.conf file
<code>
PassengerRoot /usr/lib/ruby/gems/1.8/gems/passenger-3.0.7
PassengerRuby /usr/bin/ruby1.8</code></div>
Alternative:Install passenger with Ubuntu
<code>
$apt-get install libapache2-mod-passenger
</code>

Configure Apache
<code>
$nano /etc/apache2/sites-available/redmine
</code>
adding the following
<code>
&lt;VirtualHost *:80&gt;
ServerName 127.0.0.1
ServerAdmin webmaster@localhost
DocumentRoot /var/www/redmine/public
PassengerDefaultUser www-data
RailsEnv production
RailsBaseURI /redmine
SetEnv X_DEBIAN_SITEID "default"
&lt;Directory /var/www/redmine/public&gt;
AllowOverride all
Options -MultiViews
Order allow,deny
allow from all
&lt;/Directory&gt;
&lt;/VirtualHost&gt;
</code>
<code>
$nano /etc/apache2/apache2.conf
</code>
adding the following
<code>
Include /etc/apache2/mods-available/passenger.conf
</code>

Activate your site
<code>
$ln -s /etc/apache2/sites-available/redmin /etc/apache2/sites-enabled/redmine
</code>
Remove default entry
<code>
$rm /etc/apache2/sites-enabled/000-default
</code>

Restart Apache
<code>
$/etc/init.d/apache2_restart
</code>

</div>
Step 7.Git integration(todo)
