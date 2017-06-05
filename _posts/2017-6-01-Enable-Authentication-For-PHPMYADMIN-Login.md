---
layout: post
section-type: post
title: Enable Authentication For PHPMYADMIN Login
category: sysadmin
tags: [ 'sysadmin', 'phpmyadmin', 'mysql', 'lampp' ]
--- 
Installing the lampp package for a developing enviroment is the most commo for deverloper. Accepting all the default settings during installation result in a security issue. Anyone can access the database without authentication - nothing.

To make phpmyadmin throw an authentication when being accessed, like below:

![PHPMYADMIN LOGIN]({{ site.baseurl | cdn }}/img/post/phpmyadmin.png){:class="img-responsive"}

Open the phpmyadmin configure (ex: /opt/lampp/phpmyadmin/config.inc.php ) then change:

<pre><code data-trim class="yaml">
change	$cfg['Servers'][$i]['auth_type'] = 'config';
to      $cfg['Servers'][$i]['auth_type'] = 'cookie';
</code></pre>

Save then may restart your lampp server.