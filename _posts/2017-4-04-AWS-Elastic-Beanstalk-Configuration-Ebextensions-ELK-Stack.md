---
layout: post
section-type: post
title: AWS Elastic Beanstalk Configuration Ebextensions ELK Stack
category: devops
tags: [ 'sysadmin', 'devops', 'AWS', 'Elastic Beanstalk', 'Ebextensions', 'ELK stack', 'Elasticsearch','Logstack','Kibana' ]
--- 
AWS Elastic Beanstalk is the service offered by Amazon to make your life eariser when you need to deploy applications :v

EB configuration files are so powerful that you don't have to connect to your instance through SSH to issue configuration commands. You can configure your enviroment entirely from your project source by using .ebextentions.

To start using them, create a folder called .ebextensions within the root of your project and start adding files with the .config extension to it.
![EB configuration files]({{ site.baseurl | cdn }}/img/post/eb-elk-01.png){:class="img-responsive"}
<br/>

The name of the file doesn't matter as long as the extensions .config, but keep in mind that your EB configuration files will be processed in alphabetical order. This is why it is a good idea to use numbers when naming your .config files. By naming your files appropriately, you can split your configuration activities into multiple stages.

Story:
<pre><code data-trim class="yaml">
#Mobile guy: “Hey, your API is responding I’m missing the ‘sort’ parameter, but I’m sending it!”
#API guy: “Give me 5 minutes. I need to ssh to one of our servers and find the corresponding log”
</code></pre>

When you only have one server responding to the requests, this is a manageable situation. However, when the number of servers increases you can't simply have one ssh session tailing each of the logs. This is when Elasticsearch, Logstash and Kibana come into action.

<strong>Elasticsearch</strong> is a document-oriented, high availability database that is well known for its full-text search. <br/>
<strong>Logstash</strong> is a data pipeline that helps you process logs and other event data from a variety of systems. It can connect to a variety of sources and can stream data at scale to a central analysics system. <br/>
<strong>Kibana</strong> is a tool that sits on top of Elasticsearchand allows users to query and build dashboards incredibly fast.

![ELK stack]({{ site.baseurl | cdn }}/img/post/eb-elk-02.png){:class="img-responsive"}

<strong>Writing EB Configuration Files</strong><br/>

In each EB configuration file, we'll specify one or more keys that will determine what will be done in your EB enviroment.

<strong>Add logstash to yum</strong><br/>
The first thing we'll need to setup is Logstash in the servers. ElasticBeanstalk installs packages using yum. If we want to add a new package to it, we need to explicitly do so by creating a file in /etc/yum.repos.d/.
Create a file with name 01_logstash-file.config with below details:
<pre><code data-trim class="yaml">
files:
  "/etc/yum.repos.d/logstash.repo":
    mode: "000644"
    owner: root
    group: root
    content: |
     [logstash-2.4]
     name=Logstash repository for 2.4.x packages
     baseurl=https://packages.elastic.co/logstash/2.4/centos
     gpgcheck=1
     gpgkey=https://packages.elastic.co/GPG-KEY-elasticsearch
     enabled=1

</code></pre>
<strong>Tell Elasticbeanstalk to install logstash</strong><br/>

Even though we added the package to yum, this does not install it. To do so, we create a file with name 02_logstash-config.config. This file does two key actions:
1. Create these directories: /opt/elasticbeanstalk/hooks/appdeploy/post and /opt/elasticbeanstalk/hooks/restartappserver/post. Scripts placed in these folders will be executed when they should. For example, those placed under /appdeploy/post will be executed afte the deploy has succeeded.
2. Install logstash

We'll create the file titled start_logstash.sh restarts logstash each time there is a deploy or the instance has been restarted.

<pre><code data-trim class="yaml">
commands:
  create_post_dir:
    command: "mkdir /opt/elasticbeanstalk/hooks/appdeploy/post"
    ignoreErrors: true
  create_restartappserver_post_dir:
    command: "mkdir /opt/elasticbeanstalk/hooks/restartappserver/post"
    ignoreErrors: true
  100-install-logstash:
      command: "sudo yum -y install logstash"
files:
  "/opt/elasticbeanstalk/hooks/appdeploy/post/start_logstash.sh":
    mode: "000755"
    group: root
    owner: root
    content: |
      #!/bin/bash
      for pid in `ps aux | grep /etc/logstash/conf.d | grep -v grep | tr -s ' ' | cut -d ' ' -f 2`
      do
        sudo kill -9 $pid
      done

      export HOME=/var/lib/logstash

      sudo service logstash restart


  "/opt/elasticbeanstalk/hooks/restartappserver/post/start_logstash.sh":
    mode: "000755"
    group: root
    owner: root
    content: |
      #!/bin/bash
      for pid in `ps aux | grep /etc/logstash/conf.d | grep -v grep | tr -s ' ' | cut -d ' ' -f 2`
      do
        sudo kill -9 $pid
      done

      export HOME=/var/lib/logstash

      sudo service logstash restart
</code></pre>

<strong>Add logstash configuration file</strong><br/>
So far so good. We need to tell Logstash what it is going to do. We'll define 2 inputs, 2 filters and 1 output. The file tilted 03_nginx-logstash.config

<pre><code data-trim class="yaml">
files:
  "/etc/logstash/conf.d/rails.conf":
    content: |
      input {
        file {
                        path => "/var/log/nginx/access.log"
                        type => "nginx-access"
                 }
        file {
                        path => "/var/log/nginx/error.log"
                        type => "nginx-error"
                 }
      }
	  filter {
                  if [type] == "nginx-access" {
                                grok {
                                        match => [ "message" , "%{COMBINEDAPACHELOG}+%{GREEDYDATA:extra_fields}"]
                                        overwrite => [ "message" ]
                                }

                                mutate {
                                        convert => ["response", "integer"]
                                        convert => ["bytes", "integer"]
                                        convert => ["responsetime", "float"]
                                }

                                geoip {
                                        source => "clientip"
                                        target => "geoip"
                                        add_tag => [ "nginx-geoip" ]
                                }

                                date {
                                        match => [ "timestamp" , "dd/MMM/YYYY:HH:mm:ss Z" ]
                                        remove_field => [ "timestamp" ]
                                }

                                useragent {
                                        source => "agent"
                                }
                        }
                }
          filter {
                        if [type] == "nginx-error" {
                                grok {
                                        match => [ "message" , "(?"timestamp"%{YEAR}[./-]%{MONTHNUM}[./-]%{MONTHDAY}[- ]%{TIME}) \[%{LOGLEVEL:severity}\] %{POSINT:pid}#%{NUMBER}: %{GREEDYDATA:errormessage}(?:, client: (?"client"%{IP}|%{HOSTNAME}))(?:, server: %{IPORHOST:server})(?:, request: %{QS:request})?(?:, upstream: \"%{URI:upstream}\")?(?:, host: %{QS:host})?(?:, referrer: \"%{URI:referrer}\")"]
                                        overwrite => [ "message" ]
                                }

                                geoip {
                                        source => "client"
                                        target => "geoip"
                                        add_tag => [ "nginx-geoip" ]
                                }

                                date {
                                        match => [ "timestamp" , "YYYY/MM/dd HH:mm:ss" ]
                                        remove_field => [ "timestamp" ]
                                }
                        }
                }

      output {
        elasticsearch {
                hosts => ["your elasticsearch-service-url"]
                flush_size => 1
        }
      }
</code></pre>

This file defines the following:
- <strong>Input</strong>: Nginx server's log
- <strong>Filter</strong>: Filter nginx log (access and error)
- <strong>Output</strong>: Send data to elastisearch

<strong>Building your own dashboards</strong><br/>
Kibana is incredibly flexible at the time of building dashboards. Go ahead and try to build as many as you can. Below is my sample dashboard for nginx access logs.
![ELK sample nginx logs]({{ site.baseurl | cdn }}/img/post/eb-elk-03.png){:class="img-responsive"}

Thanks for stoping by!