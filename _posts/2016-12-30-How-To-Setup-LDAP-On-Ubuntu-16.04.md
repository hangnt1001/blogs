---
layout: post
section-type: post
title: How to setup LDAP on Ubuntu 16.04
category: sysadmin
tags: [ 'sysadmin', 'ldap', 'ubuntu', 'openldap' ]
---
### How to setup LDAP on Ubuntu 16.04

In this guide, we will setup OpenLDAP server for centralized login where the users use the single account to log to multiple servers.

<strong>Install LDAP</strong>
<pre><code data-trim class="yaml">
$ sudo apt-get update
$ sudo apt-get -y install slapd ldap-utils
</code></pre>

<strong>Re-configure OpenLdap Server</strong>

The installer will automatically create an LDAP directory based on the hostname of your server which is not we want, so we are now going to reconfigure the LDAP. To do that, excute the following command.
<pre><code data-trim class="yaml">
$ sudo dpkg-reconfigure slapd
</code></pre>

There are quite a few new questions that will be asked as you go through this process. Let's go over these now:

•Omit OpenLDAP server configuration? <strong>No</strong>

•DNS domain name? 

	This option will determine the base structure of your directory path. Read the message to understand exactly how this will be implemented.

	This is actually a rather open option. You can select whatever <strong>"domain name"</strong> value you'd like, even if you don't own the actual domain. However, if you have a domain name for the server, it's probably wise to use that.

	For this guide, we're going to select domain.local for our configuration.

•Organization name?

	This is, again, pretty much entirely up to your preferences.

	For this guide, we will be using example as the name of our organization.

•Administrator password?

	As I mentioned in the installation section, this is your real opportunity to select an administrator password. Anything you select here will overwrite the previous password you used.

•Database backend? <strong>HDB</strong>

•Remove the database when slapd is purged? <strong>No</strong>

•Move old database? <strong>Yes</strong>

•Allow LDAPv2 protocol? <strong>No</strong>

At this point, your LDAP should be configured in a fairly reasonable way.

<strong>Install phpLdapadmin to manage LDAP with a web interface</strong>
<pre><code data-trim class="yaml">
$ sudo apt-get install phpldapadmin
</code></pre>

<strong>Configure phpLdapAdmin</strong>
<pre><code data-trim class="yaml">
sudo nano /etc/phpldapadmin/config.php
$servers->setValue('server','host','server_domain_name_or_IP');
$servers->setValue('server','base',array('dc=domain,dc=local'));
$servers->setValue('login','bind_id','cn=admin,dc=domain,dc=local');
</code></pre>

Secure Apache

Create a password authentication file

<pre><code data-trim class="yaml">
sudo apt-get install apache2-utils
sudo htpasswd -c /etc/apache2/htpasswd demo_use
</code></pre>

Setup the location block that will implement our password protection for the entire phpLdapadmin installation

<pre><code data-trim class="yaml">
<Location /superldap>
    AuthType Basic
    AuthName "Restricted Files"
    AuthUserFile /etc/apache2/htpasswd
    Require valid-user
</Location>
</code></pre>

Restart Apache

<pre><code data-trim class="yaml">
sudo service apache2 restart
</code></pre>

![ldap]({{ site.baseurl | cdn }}/img/post/ldap01.png){:class="img-responsive"}

At this point, you are logged into the phpLDAPAdmin Interface. You have ability to add users, organizational units, groups, and relationships.
