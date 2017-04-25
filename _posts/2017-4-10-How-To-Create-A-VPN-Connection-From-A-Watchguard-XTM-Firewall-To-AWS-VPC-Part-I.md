---
layout: post
section-type: post
title: How To Create A VPN Connection From A Watchguard XTM Firewall To AWS VPC Part I 
category: sysadmin
tags: [ 'sysadmin', 'devops', 'AWS', 'VPN', 'Watchguard XTM', 'Watchguard XTM' ]
--- 
In this tutorial, I will show you how to create a VPN connection from your network to AWS VPC so you can access our cloud instance over a private network.

In this example we will use the following address scheme:
Our office network: 192.168.1.0/24
AWS VPC network: 172.16.0.0/16

<strong>Part I: Configure on AWS</strong>
<strong>1. Create a Customer Gateway</strong>
- Log into <strong>AWS</strong>, and go to <strong>Networking > VPC</strong>
- Under <strong>VPN Connections > Customer Gateways</strong> create a Customer Gateway and label your external site identifier, enter in your Watchguard firewall IP address and specify outing as Static
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn01.PNG){:class="img-responsive"}
<br/>
<strong>2. Create a Virtual Private Gateway</strong>
- Go to <strong>VPN Connections > Virtual Private Gateways</strong> and create a Virtual Private Gateway for your network exit point for your region.
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn02.PNG){:class="img-responsive"}
<br/>
- Attach the <strong>VPC 172.31.0.0/16</strong> to this gateway
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn03.PNG){:class="img-responsive"}
<br/>
<strong>3. Create VPN Connection</strong>
- Give the connection a name, and assign it the Virtual Private Gateway and Customer Gateway from previous steps
- Specify the routing as Static and enter in your internal network CIDR block so AWS VPC would know which subnets to route to your internal network
Note: Once you click “Yes, Create” AWS will start billing you for IPSec connections
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn04.PNG){:class="img-responsive"}
<br/>
- Add Static Route to your internal network if you do not see it
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn05.PNG){:class="img-responsive"}
<br/>
<strong>4. Download Configuration</strong>
- Right Click on the VPN Connection that you created and select “Download Configuration”
- Select <strong>Generic</strong>
- This file will include VPN settings and secret keys you have to apply on your Watchguard firewall.
