---
layout: post
section-type: post
title: How To Force HTTPS in Nginx Behind an AWS Load Balancer
category: sysadmin
tags: [ 'sysadmin', 'devops', 'aws', 'load balancer', 'https', 'nginx', 'ssl' ]
--- 

<strong>Always-On HTTPS With Nginx Behind an ELB</strong>

![AWS lambda]({{ site.baseurl | cdn }}/img/post/aws-elb-ssl02.png){:class="img-responsive"}

In this scenario, we have an ELB accepting HTTPS traffic and pass it to NGINX servers listening on port 80. We can load the SSL cert directly on the ELB and it can handle the secure traffic for all server instances behind that balancer. This removes the need to manage the cert on each instance, which is nice. And we want Nginx to force all requests that were not originally made with HTTPS to redirect to the same URL on HTTPS, execpt requests for the health check, which the ELB will make directly over HTTP.

First of all, attach your SSL cert to the load balancer. And then configure both port 80 and 443 to send traffic to each instance through port 80 like this:

![AWS lambda]({{ site.baseurl | cdn }}/img/post/aws-elb-ssl01.png){:class="img-responsive"}

In your Nginx site configuration file, add a single server block listening on port 80 since all traffic is now flowing through that port. Now you only need to a bit to check for HTTPS and redirect if it's not.

<pre><code data-trim class="yaml">
	server {
   # all traffic, secure or otherwise, comes in on 80 from the ELB
   listen 80;
 
   # ELB stores the protocol used between the client 
   # and the load balancer in the X-Forwarded-Proto request header.
   # Check for 'https' and redirect if not
   if ($http_x_forwarded_proto != "https") {
      rewrite ^(.*)$ https://$server_name$1 permanent;
    }
 
   server_name  your-secure-site.com  www.your-secure-site.com;
 
.... (the rest of your config)
   }
</code></pre>