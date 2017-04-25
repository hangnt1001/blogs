---
layout: post
section-type: post
title: How To Create A VPN Connection From A Watchguard XTM Firewall To AWS VPC Part II 
category: sysadmin
tags: [ 'sysadmin', 'devops', 'AWS', 'VPN', 'Watchguard XTM', 'Watchguard XTM' ]
--- 
From the part I, we already have the configuration file. This part we will configur watchguard firewall.
<br/>
- Start up your Watchguard System Manager
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn07.PNG){:class="img-responsive"}
<br/>
- Policy Manager: click on policy manager then select <strong>VPN -> Branch Office Gateways</strong>
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn08.PNG){:class="img-responsive"}
<br/>
- Add Gateway
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn09.PNG){:class="img-responsive"}
<br/>
- New Gateway: This section is populated with information from the file you downloaded from Amazon. The preshared key and the gateway endpoints. Make sure the Start Phase 1 tunnel when Firebox starts is checked
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn10.PNG){:class="img-responsive"}
<br/>
- Gateway Endpoint: Using the data from the Amazon file input your external ISP IP address and the external gateway IP from the Amazon file.
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn11.PNG){:class="img-responsive"}
<br/>

- Phase 1
Click the Phase 1 tab. 
Pick a name for your Gateway. 
Mode=Main *updated for XTM 11.10.2.B481673* (If main fails you can try aggressive) 
Fill out the rest of it with the same settings in the screenshot. 
Click Ok to save the gateway settings.
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn12.PNG){:class="img-responsive"}
<br/>

- Click the VPN tab in Policy Manager: Select the Branch Office Tunnels to add a new tunnel.
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn13.PNG){:class="img-responsive"}
<br/>
- Click Add Button: Click the add button to bring up the new tunnel dialogue box.
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn14.PNG){:class="img-responsive"}
<br/>
- New Tunnel
Name your tunnel. 
Select the gateway you made in the previous steps.
Under Addresses click add. 
Then add local 192.168.1.0/24 <===> 172.16.0.0/16 
172.16.0.0/16 is the subnet of your VPC at Amazon 
(<==> = both directions) 
Check the box Add this tunnel to the BOVPN-Allow polices.
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn15.PNG){:class="img-responsive"}
<br/>
- Phase 2
Click the Phase 2 settings check the PFS box and select Diffe-Hellman Group2. 
Under the IPSec Proposals click Add. 
Select the Create a new Phase2 Proposal and give it the information from your Amazon file 
Then click Ok and upload your configuration file to your firewall.
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn16.PNG){:class="img-responsive"}
<br/>
- Checking if there is a Gateway/Tunnel: Go back to your System manager and expand the Branch Office Tunnels. You should see your Gateway and Tunnel listed now. 
I went ahead and re-keyed my tunnel at this point.
![VPN AWS]({{ site.baseurl | cdn }}/img/post/aws-vpn17.PNG){:class="img-responsive"}
<br/>
- Re-keying the tunnel  
Right click over the Branch Office Tunnels and select re-key all tunnels. It will ask you for your admin password to your Watchguard unit.