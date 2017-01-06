---
layout: post
section-type: post
title: Setting up portal to help users to change their password in an LDAP directory
category: sysadmin 
tags: [ 'sysadmin', 'ldap', 'ubuntu' ]
--- 

### Setting up portal to help users to change their password in an LDAP directory

After installed <a href="https://gluesolution.xyz/sysadmin/2016/12/30/How-To-Setup-LDAP-On-Ubuntu-16.04.html">OpenLdap</a>. I need to setup the portal to help users to change their password. I was thinking to code some scripts to help users easier to change their password.

Lukily, I found the tool to help me. It's Self Service password which is a PHP application. This Application can be used on standard LDAPv3 directories (OpenLDAP, OpenDS, ApacheDS, Oracle DSEE, Novell, etc.) and also on Active Directory.
apt-get update
#apt-get install self-service-password
I am using Ubuntu 16.04 which is installed Apache and PHP.

Add repository and install the packages

<pre><code data-trim class="yaml">
#nano /etc/apt/sources.list.d/ltb-project.list
deb [arch=amd64] http://ltb-project.org/debian/jessie jessie main

#wget -O - http://ltb-project.org/wiki/lib/RPM-GPG-KEY-LTB-project | sudo apt-key add -
#apt-get update
#apt-get install self-service-password
</code></pre>

we may need to install php-ldap php-xml php-mcrypt

Copy the default SSP root directory to the new directory

<pre><code data-trim class="yaml">
cp -rpv /usr/share/self-service-password /var/www/self
</code></pre>

Edit the Apache configuration to use the source code above

Configure SSP to connect to LDAP

<pre><code data-trim class="yaml">
nano /var/www/self/conf/config.inc.php
# LDAP
$ldap_url = "ldap://localhost"; #hostname or ip of LDAP server
$ldap_binddn = "cn=ldap,dc=local,dc=domain";
$ldap_bindpw = "password";
$ldap_base = "dc=local,dc=domain";
$ldap_login_attribute = "uid";
$ldap_fullname_attribute = "cn";
$ldap_filter = "(&(objectClass=person)($ldap_login_attribute={login}))";
 
$hash = "MD5";
.....
</code></pre>

Ok, let change your own password to the new one

![LDAP password]({{ site.baseurl | cdn }}/img/post/password1.png){:class="img-responsive"}