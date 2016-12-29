---
layout: post
section-type: post
title: How To Move Jenkins To Another Server
category: devops
tags: [ 'jenkins', 'ci', 'backup', 'devops','restore' ]
---
###How to move jenkins to another server
1. Stop both Jenkins server
2. Copy <strong>JENKINS_HOME</strong> (e.g. /var/lib/jenkins) from the old server to the new one. From a console in the new server:
<pre><code data-trim class="yaml">
rsync -av username@old-server-ip:/var/lib/jenkins/ /var/lib/jenkins/
</code></pre>
3. Start your new jenkins server

You minght not need this, but I had to
* <strong>Manage Jenkins</strong> and <strong>Reload Configuration fromm Disk</strong>
* Disconnect and connect all the slaves again
* Check that in the <strong>Configure System -> Jenkins Location</strong>, the <strong>Jenkins URL</strong> is correctly assigned to the new Jenkins server.