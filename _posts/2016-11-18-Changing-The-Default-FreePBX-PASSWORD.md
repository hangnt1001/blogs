---
layout: post
section-type: post
title: changing the default FreePBX password
category: tech
tags: [ 'voip', 'asterisk', 'freepbx', 'password' ]
---
### How to change the default FreePBX Asterisk MySQL database password:

From the ssh shell:
<pre><code data-trim class="yaml">
mysqladmin -u asteriskuser -p password newpass
</code></pre>
You can find the current database username and password in:
<pre><code data-trim class="yaml">
/etc/amportal.conf 
</code></pre>
Now verify that the new password works:
<pre><code data-trim class="yaml">
mysql -u asteriskuser -p
</code></pre>
Once you change the password using mysqladmin, you will need to modify
<pre><code data-trim class="yaml"> 
/etc/amportal.conf 
</code></pre>
to also use the new password.  

We suggest making a copy of:
<pre><code data-trim class="yaml">
/etc/amportal.conf 
</code></pre>
before you edit it.

Simply type:
<pre><code data-trim class="yaml">
cp /etc/amportal.conf /etc/amportal.conf.bak
</code></pre>
at the shell prompt to copy the current amportal.conf file into a new file called amportal.conf.bak

Inside /etc/amportal.conf you will want to check these two lines:
<pre><code data-trim class="yaml">
AMPDBUSER=asteriskuser
AMPDBPASS=newpass
</code></pre>
Make sure that they both match the username and password (new password) you set above.

There are two more files you should modify in the same way:
<pre><code data-trim class="yaml">
/etc/asterisk/cdr_mysql.conf:
</code></pre>
check these two lines:
<pre><code data-trim class="yaml">
password=newpass
user=asteriskuser
</code></pre>
And:
<pre><code data-trim class="yaml">
/etc/asterisk/res_mysql.conf:

Check these two lines:
dbuser = asteriskuser
dbpass = newpass
</code></pre>