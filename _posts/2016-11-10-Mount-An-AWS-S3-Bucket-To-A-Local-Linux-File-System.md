---
layout: post
section-type: post
title: Mount An AWS S3 Bucket To A Local Linux File System
category: tech
tags: [ 'aws', 's3', 'bucket', 'linux','s3fs','ubuntu','nginx' ]
---

I'm using s3fs which allows Linux and Mac OS X to mount an S3 bucket via FUSE. 

INSTALLATION

For Ubuntu 14.04 only and nginx, modify yourself for other distros, web services.

Install dependencies:
<pre><code data-trim class="yaml">
sudo apt-get update
sudo apt-get install automake autotools-dev g++ git libcurl4-gnutls-dev libfuse-dev libssl-dev libxml2-dev make pkg-config
</code></pre>

Let's build s3fs:
<pre><code data-trim class="yaml">
git clone https://github.com/s3fs-fuse/s3fs-fuse.git
cd s3fs-fuse
./autogen.sh
./configure
make
sudo make install
</code></pre>

Create a file storing the AWS keys required by S3 to allow your s3fs requests:
<pre><code data-trim class="yaml">
echo ACCESS_KEY_ID:SECRET_ACCESS_KEY/ > /path/to/s3_passwd
chmod 600 /path/to/s3_passwd
</code></pre>

We are now ready to mount your S3 bucket. Mount the S3 bucket using the www-data user permission. Uid=33 and gid=33 is an example.
<pre><code data-trim class="yaml">
mkdir /path/to/mouth/
s3fs -o allow_other,uid=33,gid=33,use_cache=/etc/ -o passwd_file=/path/to/s3_passwd bucket_name /path/to/mount/
</code></pre>

Manual unmount the virtual drive using umount command:
<pre><code data-trim class="yaml">
umount /path/to/mouth
</code></pre>

Automatically mouth the S3 bucket when the server boots by adding an entry to /etc/fstab using the following command:
<pre><code data-trim class="yaml">
bucket_name /path/to/mount fuse.s3fs _netdev,allow_other,uid=33,gid=33 0 0
</code></pre>

