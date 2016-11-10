---
layout: post
section-type: post
title: How to convert a .pfx SSL certificate to .key/.crt file
category: tech
tags: [ 'ssl', 'pem', 'crt', 'key','pfx', 'certificate','OpenSSL' ]
---

I'm using OpenSSL which can be download <a href="https://www.openssl.org/source/">here</a> to convert SSL certificate. 

After installed OpenSSL, fire up a command prompt and cd to the folder that contains your .pfx file. First type the first command to extract the private key.
<pre><code data-trim class="yaml">
openssl pkcs12 -in [yourfile.pfx] -nocerts -out [keyfile-encrypted.key]
</code></pre>
Onee entered you need to type in the import password of the .pfx file. This is the password that you used to protect your keypair when you created your .pfx file. Once you entered the import password OpenSSL requests you to type in another password, twice! This new password will protect your .key file.

Now, let's extract the certificate.
<pre><code data-trim class="yaml">
openssl pkcs12 -in [yourfile.pfx] -clcerts -nokeys -out [certificate.crt]
</code></pre>

Just press enter and your certificate appears.

To decrypt the key above, we can use this command.
<pre><code data-trim class="yaml">
openssl rsa -in [keyfile-encrypted.key] -out [keyfile-decrypted.key]
</code></pre>

In some cases, you might be forced to convert your private key to PEM format. You can do so with the following command.
<pre><code data-trim class="yaml">
openssl rsa -in [keyfile-encrypted.key] -outform PEM -out [keyfile-encrypted-pem.key]
</code></pre>

Good luck!