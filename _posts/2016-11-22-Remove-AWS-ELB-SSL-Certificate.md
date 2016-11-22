---
layout: post
section-type: post
title: Remove AWS ELB SSL Certificate
category: sysadmin
tags: [ 'aws', 'elb', 'ssl', 'certificate','iam', 'load balancer']
---
### How to remove AWS ELB SSL Certificate

In order to delete the certificate which we have applied to an ELB, we will need to delete the ELB or change the certificate of the ELB.

We can do this in the Management Console by selecting the ELB in the Load Balancer section of the console and selecting the delete option.

We may need to take the additional steps of removing the certificate from the IAM policy by using the iam API command.

To make sure we delete the right certificate, we should found the correct name by command:
<pre><code data-trim class="yaml">
aws iam list-server-certificates
</code></pre> 

Then delete the certificate by command
<pre><code data-trim class="yaml">
aws iam delete-server-certificate --server-certificate-name nameofcertificate
</code></pre> 
