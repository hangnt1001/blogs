---
layout: post
section-type: post
title: How to configure VPN failover from Watchguard XTM Firewall to AWS
category: sysadmin
tags: [ 'sysadmin', 'vpn', 'failover', 'watchguard', 'firewall', 'aws', 'vpc' ]
--- 

From this post <a href="https://gluesolution.xyz/sysadmin/2017/04/10/How-To-Create-A-VPN-Connection-From-A-Watchguard-XTM-Firewall-To-AWS-VPC-Part-I.html">HOW TO CREATE A VPN CONNECTION FROM A WATCHGUARD XTM FIREWALL TO AWS VPC PART I</a> and <a href="https://gluesolution.xyz/sysadmin/2017/04/20/How-To-Create-A-VPN-Connection-From-A-Watchguard-XTM-Firewall-To-AWS-VPC-Part-II.html">HOW TO CREATE A VPN CONNECTION FROM A WATCHGUARD XTM FIREWALL TO AWS VPC PART II</a>, We've configured VPN connection from watchguard xtm firewall to AWS VPC. But some case, we need to configure failover VPN connection to connect your firebox to an AWS virtual network for greater flexibility and networking capabilities, to do that our firebox need to run fireware OS v11.12.2 or higher.

In this post, we've created VPN connection on AWS and downloaded the configuration file.
In your AWS VPN configuration, the setting are:
<pre><code data-trim class="yaml">
Customer Gateway Address — 203.0.113.2 (external interface on the Firebox )
VPN Connections:
Tunnel 1 — 198.51.100.2 (first IP address of the AWS virtual private gateway)
Tunnel 2 — 192.0.2.2 (second IP address of the AWS virtual private gateway)
Static Route — 10.0.1.0/24 (trusted network of the Firebox)
</code></pre>

<strong>Firebox Interfaces</strong>
For this example, the Firebox has one external interface and one trusted network.

<pre><code data-trim class="yaml">
Interface	Type	Name	IP Address
0	External	External	203.0.113.2/24
1	Trusted	Trusted	10.0.1.1/24
</code></pre>

<strong>AWS Interfaces</strong>
For this example, the AWS VPN configuration has two external virtual interfaces and one trusted virtual network.

An AWS VPN configuration includes one virtual private gateway with two external IP addresses for redundancy. AWS automatically determines which IP address is the primary IP address.

Failover between the external IP addresses is enabled by default. If the primary AWS external IP address is unavailable, VPN traffic automatically fails over to the other AWS external IP address.

<pre><code data-trim class="yaml">
Interface	Type	Name	IP Address
0	External	External1	198.51.100.2/24
1	External	External2	192.0.2.2/24
2	Trusted	Trusted	10.0.100.1/24
</code></pre>

<strong>Firebox configuration</strong>
To configure a redundant gateway that uses both AWS external IP addresses, you must configure one BOVPN virtual interface that includes two gateway endpoints.

You must specify different pre-shared keys for each gateway endpoint on your Firebox, which requires Fireware v11.12.2 or higher.

On the <strong>Gateway Settings</strong> tab:

<strong>Remote Endpoint Type</strong> is <strong>Cloud VPN or Third-Party Gateway</strong>
<strong>Credential Method</strong> is <strong>Use Pre-Shared Key</strong>. 
Keep the text box empty. Later in the configuration process, you must specify a different pre-shared key for each gateway in the gateway endpoint settings.
<strong>Gateway Endpoint</strong> settings are:
<strong>Local Gateway ID</strong> — 203.0.113.2 (external interface of the Firebox)
<strong>Remote Gateway IP address and ID</strong> — 198.51.100.2 (first IP address of the AWS virtual private gateway)
<strong>Remote Gateway IP address and ID</strong> — 192.0.2.2 (second IP address of the AWS virtual private gateway)

![Failover VPN]({{ site.baseurl | cdn }}/img/post/failover-vpn-01.jpg){:class="img-responsive"}

In <strong>Advanced</strong> settings for each gateway endpoint, specify the pre-shared key included in your AWS configuration file. The AWS configuration file includes two pre-shared keys, one for each gateway endpoint.

![Failover VPN]({{ site.baseurl | cdn }}/img/post/failover-vpn-02.jpg){:class="img-responsive"}

On the <strong>VPN Routes</strong> tab, specify the route to the trusted (private) network of your AWS VPC. To find this IP address in your AWS configuration, select <strong>Subnets</strong> or <strong>Route Tables</strong>.

<pre><code data-trim class="yaml">
Choose Type — Network IPv4
Route to — 10.0.100.0/24
</code></pre>

![Failover VPN]({{ site.baseurl | cdn }}/img/post/failover-vpn-03.jpg){:class="img-responsive"}

During VPN negotiations, AWS identifies the authentication and encryption algorithm settings from the Firebox and automatically uses the same settings, if they are supported. AWS supports specific proposals. You cannot edit the list of proposals available in AWS.

On the <strong>Phase 1 Settings</strong> tab for both gateways, we recommend these settings for stronger security:

<pre><code data-trim class="yaml">
Authentication — SHA1-256
Encryption — AES (256-bit)
</code></pre>

Keep all other Phase 1 settings at the default values. Do not change the default Version setting of IKEv1. AWS does not support IKEv2.

![Failover VPN]({{ site.baseurl | cdn }}/img/post/failover-vpn-04.png){:class="img-responsive"}

For stronger security, we recommend that you add a new Phase 2 proposal that specifies ESP, AES (256-bit) for encryption, and SHA1-256 for authentication. 

On the <strong>Phase 2 Settings</strong> tab for both gateways:

<pre><code data-trim class="yaml">
Enable Perfect Forward Secrecy — Selected
Phase 2 IPSec Proposal — The ESP-AES256-SHA2-256 proposal that you added
</code></pre>

![Failover VPN]({{ site.baseurl | cdn }}/img/post/failover-vpn-05.png){:class="img-responsive"}

After configured, you will see the status like below:

![Failover VPN]({{ site.baseurl | cdn }}/img/post/failover-vpn-06.png){:class="img-responsive"}
