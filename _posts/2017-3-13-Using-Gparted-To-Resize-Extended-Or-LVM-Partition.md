---
layout: post
section-type: post
title: Using gparted to resize extended or LVM partition
category: sysadmin
tags: [ 'sysadmin', 'gparted', 'LVM', ]
--- 

In this post, I will show you how to use gparted to resize extended or LVM partition. Be a sysadmins, we have a lot of troubles relate to manage disk on linux. To make it easy, I often use Gparted which is a free partition editor for graphically managing your disk partitions.

First of all, we have to download gparted then insert and make first boot on CD.
Below is an example, we are running out partition /dev/sda2 and want to extend unallocated volumn 

![Resize LVM partition]({{ site.baseurl | cdn }}/img/post/gparted1.png){:class="img-responsive"}

<br/> 

1. Right click both /dev/sda2 and /dev/sda5 and choose "Deactivate"
2. Resize the extended (/dev/sda2) partition 
3. Resize the LVM (/dev/sda5) partition
4. Run below commands to expand LVM to all remaining free space:

<pre><code data-trim class="yaml">
lvextend –l +100%FREE [MOUNTPOINT]
</code></pre>

then expand filesystem

<pre><code data-trim class="yaml">
sudo resize2fs [MOUNTPOINT]
</code></pre>

Thanks