---
layout: post
section-type: post
title: Disconnect From Domain And Keep User Profile For Local User
category: tech
tags: [ 'domain', 'user', 'profile', 'local' ]
---

### Disconnect from domain and keep user profile for local user

Have you ever been annoyed when you disconnect a machine from a domain and wanted to keep the profile for your local user?

Let's start

<strong>1. Backup user profile</strong>
First you should log in as the domain admin then make a copy of the user profile you are going to mess around with (you may need to reboot your machine if the user profile has been in use).

<strong>2. Download userprofile wizard program</strong>
Go to the following website and download a program User Profile Wizard.

<a href="http://www.forensit.com/domain-migration.html">http://www.forensit.com/domain-migration.html</a>

<strong>3. Disconnect the domain</strong>
![Disconnect the domain]({{ site.baseurl | cdn }}/img/post/domain1.png){:class="img-responsive"}

Disconnect your machine from the domain, make sure you have a local user profile setup on the machine with admin access so you can log back in afterwards, then reboot machine.

<strong>4. User Profile Wizard user account info</strong>
![Profile wizard]({{ site.baseurl | cdn }}/img/post/domain2.png){:class="img-responsive"}

Click the next button for the first screen, then enter the local PC info or hit the drop down box and select from list.

Enter the local username you created or already have and set as default logon, then click next.

<strong>5. Select Profile Path</strong>
![Profile Path]({{ site.baseurl | cdn }}/img/post/domain3.png){:class="img-responsive"}

The next screen will give you a list of current user profiles, to find the old domain profile you need to check the show unassigned profiles box.

You might find the name to the left is not very helpful but you should be able to find the profle you want from the path name to the right.

Click next

<strong>6. Migration complete</strong>
![Migration complete]({{ site.baseurl | cdn }}/img/post/domain4.png){:class="img-responsive"}

On the next screen you should see lots of info about changing various settings and eventually config complete.

Click next then finish.

The machine may not ask to reboot give it a couple of minutes to make sure it's finished then reboot.
