---
layout: post
section-type: post
title: How To Set Default File Permissions For All Folders Files In A Directory
category: sysadmin
tags: [ 'sysadmin', 'linux', 'files', 'permission' ]
--- 

Sometimes, we want to set a folder such that anything created within it (directories, files) inherit default permissions and group.

Lets call the group "media". And also, the folders/files created within the directory should have g+rw automatically.

<strong>To archive this</strong>, we will use two tools:
<strong>GID</strong> By setting the GID on the directory, files created within this directory will have the same group.

<pre><code data-trim class="yaml">
chmod g+s directory  //set gid 
</code></pre>

<strong>ACL (man acl)</strong> We can now use ACL to create default permissions for newly created files in the directory

<pre><code data-trim class="yaml">
setfacl -d -m g::rwx /directory  //set group to rwx default 
setfacl -d -m o::rx /directory  //set other
</code></pre>

Check the new permission:
<pre><code data-trim class="yaml">
getfacl /directory
</code></pre>

Output:
<pre><code data-trim class="yaml">
# file: ../<directory>/
# owner: <user>
# group: media
# flags: -s-
user::rwx
group::rwx
other::r-x
default:user::rwx
default:group::rwx
default:other::r-x
</code></pre>

