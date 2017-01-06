---
layout: post
section-type: post
title: Setting up local DNS server using bind and webmin
category: sysadmin
tags: [ 'sysadmin', 'ldap', 'ubuntu', 'dns', 'webmin' ]
---
### Setting up local DNS server using bind and webmin

<strong>The Domain Name System (DNS)</strong> is a hierarchical distributed naming system for computers, services, or any resource connected to the Internet or a private network. It associates various information with domain names assigned to each of the participating entities. A Domain Name Service resolves queries for these names into IP addresses for the purpose of locating computer services and devices worldwide.

<strong>BIND</strong> is the most widely used DNS software on the Internet. On Unix-like operating systems it is the de facto standard.

I am using Ubuntu 16.04 and the first step is to edit the host file:

<pre><code data-trim class="yaml">
nano /etc/hosts
</code></pre>

Make it more like this

<pre><code data-trim class="yaml">
127.0.0.1       localhost
192.168.1.250   ns1.example.com
</code></pre>

Install Bind9 DNS server

<pre><code data-trim class="yaml">
apt-get install bind9 bind9utils bind9-doc
</code></pre>

Add the webmin repositories to sources.list and install webmin

<pre><code data-trim class="yaml">
echo "deb http://download.webmin.com/download/repository sarge contrib" >> /etc/apt/sources.list.d/webmin.list
echo "deb http://webmin.mirror.somersettechsolutions.co.uk/repository sarge contrib" >> /etc/apt/sources.list.d/webmin.list
wget -O - http://www.webmin.com/jcameron-key.asc | apt-key add -
apt-get update
apt-get install -y webmin
</code></pre>

After the installation you can login here

<pre><code data-trim class="yaml">
https://ns1.example.com or https://192.168.1.250
</code></pre>

Browse Webmin->Webmin Servers Index to configure the webmin server.

Basic setup of DNS server

	Create master zone: Servers -> BIND DNS Server -> Create Master Zone

	Again to create the reserver zone

	Create forwarding and transfers: Servers -> BIND DNS Server	-> Forwarding and Transfer

	Now, we can add the server record.

Here is the example of setting tup.

![BIND DNS]({{ site.baseurl | cdn }}/img/post/dns1.png){:class="img-responsive"}

Next post, we may set up the secondary DNS.