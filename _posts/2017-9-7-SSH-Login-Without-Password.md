---
layout: post
section-type: post
title: SSH LOGIN WITHOUT PASSWORD
category: sysadmin
tags: [ 'sysadmin', 'ssh', 'login', 'nopassword' , 'authentication']
--- 
Public key authentication allows you to login to a remote host via the SSH protocol without a password and is more secure than password-based authentication.

<strong>Create Key</strong>

At the local server
<pre><code data-trim class="yaml">
local-server$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa):
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/user/.ssh/id_rsa.
Your public key has been saved in /home/user/.ssh/id_rsa.pub.
The key fingerprint is:
b2:ad:a0:80:85:ad:6c:16:bd:1c:e7:63:4f:a0:00:15 user@host
The key's randomart image is:
+--[ RSA 2048]----+
|  E.             |
| .               |
|.                |
|.o.              |
|.ooo o. S        |
|oo+ * .+         |
|++ +.+...        |
|o. ...+.         |
|  .   ..         |
+-----------------+
</code></pre>

For added security '''the key itself''' would be protected using a strong ''passphrase''. If a passphrase is used to protect the key, ssh-agent can be used to cache the passphrase.

<strong>Copy key to remote server</strong>

<pre><code data-trim class="yaml">
local-server$ ssh-copy-id glue@gluesolution.xyz
glue@gluesolution.xyz's password:
Now try logging into the machine, with "ssh 'glue@gluesolution.xyz'", and check in:

  .ssh/authorized_keys

to make sure we haven't added extra keys that you weren't expecting.
</code></pre>

<strong>Login to remote server</strong>

There is no password is required

<pre><code data-trim class="yaml">
local-server$ ssh glue@gluesolution.xyz
Last login: Fri Jul  7 12:47:53 2017 from 192.168.1.39
</code></pre>

Cheers