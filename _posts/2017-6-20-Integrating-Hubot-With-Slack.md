---
layout: post
section-type: post
title: Integrating Hubot with Slack
category: devops
tags: [ 'sysadmin', 'devops', 'hubot', 'slack', 'hubot-control', 'bot', 'slack-bot' ]
--- 
From the previous post <a href="https://gluesolution.xyz/devops/2017/06/19/How-To-Install-Hubot-Control-On-Ubuntu-Linux.html">How to install Hubot-Control on Ubuntu Linux</a>, we've created Hubot-control to help us configure and manage multiple Hubot instances using a web interface.

In this post, we will integrate Hubot with slack. Slack is a team collaboration tool which offers persistent chat rooms, as well as private groups and direct messaging. 

The first thing, we need to do is install the slack adapter in our project
<pre><code data-trim class="yaml">
$ npm install hubot-slack --save
</code></pre>

Once that is done, you will also need to setup a Custom Bot on your Slack team. This will create a token that your hubot can use to log into your team as a bot. Visit the <a href="https://mediastep.slack.com/apps/A0F7YS25R-bots">Custom Bot creation page</a> to register your bot with your Slack team, and to retrieve a new bot token.

<strong>Running Hubot</strong> <br/>
Open hubot-control page, at the hubot's name, click configure button then add the hubot_slack_token at the Variables tab.
![Hubot Slack]({{ site.baseurl | cdn }}/img/post/hubot-slack-1.png){:class="img-responsive"}

Restart the bot, you'll then be able to test out your script inside Slack app
![Hubot Slack]({{ site.baseurl | cdn }}/img/post/hubot-slack-2.png){:class="img-responsive"}

Next step, we will build the amazing things call ChatOps.