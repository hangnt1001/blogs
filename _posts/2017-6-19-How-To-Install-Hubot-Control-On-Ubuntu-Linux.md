---
layout: post
section-type: post
title: How to install Hubot Control on Ubuntu Linux
category: devops
tags: [ 'sysadmin', 'devops', 'hubot', 'slack', 'hubot-control', 'linux', 'ubuntu', 'chatops', 'chatbot' ]
--- 
<strong>What is Hubot and why should I want it?</strong>
Hubot is an awesome chat bot open sourced by Github. It's easily extendable using CoffeeScript, and you can run it on pretty much every popular messaging service using a variety of adapters. However, Hubot can be quite tricky configure and operate, and that's where Hubot control comes in - it allows you to configure and manage multiple Hubot instances using a web interface.

<strong>Prepare your system</strong>
We will be doing the setup on Ubuntu 16.04 where installed node and npm.

<pre><code data-trim class="yaml">
##Installing nodejs
$ sudo curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ sudo apt-get install -y nodejs

$ node -v
$ v8.1.2

$ npm -v
$ 5.0.3

##Updating npm
$ sudo npm install npm@latest -g
</code></pre>

<strong>Install system dependencies</strong>
<pre><code data-trim class="yaml">
## Unzip package
$ sudo apt-get install unzip
## Ruby bundler
$ sudo apt-get install ruby-bundler
</code></pre>

<strong>Prepare Hubot Control source</strong>
Hubot Control is a plain and simple Ruby on Rails webapp, there is no native packaging available, so we will get the source code to our /opt directory:

<pre><code data-trim class="yaml">
$ cd /opt
$ sudo wget https://github.com/spajus/hubot-control/archive/master.zip

## Unzip source code
$ sudo unzip master.zip

## Change name of folder master
$ sudo mv hubot-control-master/ hubot-control/

## Required node packages
$ npm install -g coffee-script
$ npm install -g hubot
$ npm install -g yo generator-hubot
</code></pre>

<strong>Add "hubot" user</strong>
Running things as root is a very dangerous practice, so let's set up a fresh user just for Hubot, pointing it's home directory to /opt/hubot-control. I am using current user as hubot user

<pre><code data-trim class="yaml">
$sudo chown -R hangnt:hangnt /opt/hubot-control
</code></pre>

<strong>Install RVM and Ruby 2.0.0</strong>
We will install RVM to get desired version of Ruby without messing up the system As current user
<pre><code data-trim class="yaml">
$ \curl -sSL https://get.rvm.io 

# Will load RVM for the first time.
$ source ~/.rvm/scripts/rvm 

# Install Ruby
$ sudo apt-get install ruby
$ ruby -v
$ ruby 2.3.1p112 (2016-04-26) [x86_64-linux-gnu]
</code></pre>

<strong>configure the database</strong>
You can configure Hubot Control to use any database you want. In this guide, we will be using SQLite in development mode, so it will work out of the box. However, to avoid installing PostgreSQL gem, which requires development libraries, we will remove gem 'pg' line from /opt/hubot-control/Gemfile, like so:

<pre><code data-trim class="yaml">
$ sed -i "/gem 'pg'/d" /opt/hubot-control/Gemfile
</code></pre>

<strong>Install Ruby Gems</strong>
To be able to start Hubot Control, you have to install the dependencies.

<pre><code data-trim class="yaml">
$ cd /opt/hubot-control
$ bundle install
</code></pre>

You may need to edit Ruby version to 2.3.1 (the latest version that you installed) on Gemfile and install ruby 2.3.1 or bundle
<pre><code data-trim class="yaml">
$ rvm install ruby-2.3.1
$ gem install bundler
</code></pre>

<strong>Prepare the database</strong>
A final step before starting Hubot Control for the first time is to create your database. If it's properly configured, this will succeed, and you will be good to go:
<pre><code data-trim class="yaml">
$ rake db:migrate
</code></pre>

<strong>Run Hubot Control</strong>
Now it's time to run Hubot Control for the first time. We will do it in a straghtforward way, by running:
<pre><code data-trim class="yaml">
$ cd /opt/hubot-control
$ rails server --port=3000
</code></pre>

Rails will boot and you will see all the output right in your console. Now point your browser to http://your-server-ip:3000/status, login with "admin@hubot-control.org / hubot", and if you see a green heading saying "You can run Hubot" - you're almost there. If something is missing, you will see the cause along with instructions what to do.

![Hubot Control]({{ site.baseurl | cdn }}/img/post/hubot-control-1.png){:class="img-responsive"}

When you will get everything running, you can start Hubot Control as a deamon using:

<pre><code data-trim class="yaml">
$ unicorn_rails -p portnumber -D
</code></pre> 

<strong>Create a Hubot</strong>
To create a new Hubot, click "Add Hubot" and fill out the form.

![Hubot Control]({{ site.baseurl | cdn }}/img/post/hubot-control-2.png){:class="img-responsive"}

Now wait patiently for a while until you see a congratulatory screen with log output. Click "Control hubot" button to proceed with further configuration.
![Hubot Control]({{ site.baseurl | cdn }}/img/post/hubot-control-3.png){:class="img-responsive"}

Start your bot and make awesome script
![Hubot Control]({{ site.baseurl | cdn }}/img/post/hubot-control-4.png){:class="img-responsive"}
<strong>Bug</strong>
You may need to add read permisson for file ~/.config/configstore/insight-yo.json.

Enjoying!


