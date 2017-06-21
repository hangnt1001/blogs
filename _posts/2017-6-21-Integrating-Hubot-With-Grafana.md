---
layout: post
section-type: post
title: Integrating Hubot with Grafana
category: devops
tags: [ 'sysadmin', 'devops', 'hubot', 'slack', 'hubot-control', 'bot', 'slack-bot', 'Grafana' ]
--- 

From the previous post <a href="https://gluesolution.xyz/devops/2017/06/19/How-To-Install-Hubot-Control-On-Ubuntu-Linux.html">How to install Hubot-Control on Ubuntu Linux</a>, we've created Hubot-control to help us configure and manage multiple Hubot instances using a web interface. And from post <a href="https://gluesolution.xyz/devops/2017/06/20/Integrating-Hubot-With-Slack.html">Integrating Hubot with Slack</a>, we've integated our hubot with slack. Now, we start integrating our hubot with grafana which is one of the best open platform for analytics and monitoring.

<br/>
Grafana 2.0 shipped with a great feature that enables it to render any graph or panel to a PNG image. No matter what data source you are using, the PNG image of the Graph will look the same as it does in your browser.

This guide will show you how to install and configure the Hubot-grafana plugin. This plugin allows you to tell hubot to render any dashboard or graph right from a channel in Slack. The bot will respond with an image of the graph and a link that will take you to the graph.

<strong>Amazon S3 Required:</strong> The hubot-grafana script will upload the rendered graphs to Amazon S3. This is so Slack can show them reliably (they require the image to be pulicly available)

Install hubot-grafana in our project:
<pre><code data-trim class="yaml">
$ npm install hubot-grafana --save
</code></pre>

Edit the file external-scripts.json and add hubot-grafana to the list of plugins
<pre><code data-trim class="yaml">
[
....
"hubot-grafana"
....
]
</code></pre>

<strong>Grafana server side rendering</strong><br/>
The hubot plugin will take advantage of the Grafana server side rendering feature that can render any panel on the server using phantomjs. Grafana ships with a phantomjs binary (linux only).

You need to set the environment variable Hubot_Grafana_API_KEY to a Grafana API key. 
![Hubot Grafana]({{ site.baseurl | cdn }}/img/post/hubot-grafana-1.png){:class="img-responsive"}
<strong>Configure</strong><br/>
The hubot-grafana plugin requires a number of environment variables to be set in order to work properly.

<pre><code data-trim class="yaml">
HUBOT_GRAFANA_HOST=http://urlofgrafanserver
HUBOT_GRAFANA_API_KEY=abcd01234deadbeef01234
HUBOT_GRAFANA_S3_BUCKET=mybucket
HUBOT_GRAFANA_S3_ACCESS_KEY_ID=ABCDEF123456XYZ
HUBOT_GRAFANA_S3_SECRET_ACCESS_KEY=aBcD01234dEaDbEef01234
HUBOT_GRAFANA_S3_PREFIX=graphs
HUBOT_GRAFANA_S3_REGION=ap-southeast-1
</code></pre>

<strong>Aliases</strong>

Some of the hubot commands above can lengthy and you might have to remember the dashboard slug (url id). If you have a few favorite graphs you want to be able check up on often (let's say from your mobile) you can create hubot command aliases with the hubot script hubot-alias.

<pre><code data-trim class="yaml">
$ npm install hubot-alias --save
</code></pre>

Now add hubot-alias to the list of plugins in external-scripts.json and restart hubot.

Now you can add an alias like this:
![Hubot Grafana]({{ site.baseurl | cdn }}/img/post/hubot-grafana-2.png){:class="img-responsive"}

![Hubot Grafana]({{ site.baseurl | cdn }}/img/post/hubot-grafana-3.png){:class="img-responsive"}

Enjoying! an make an amazing Chatbot.