---
layout: post
section-type: post
title: How To Delete Helm Tiller From K8s Cluster
category: devops
tags: [ 'devops', 'kubernetes', 'helm', 'tiller']
--- 

<strong>How to delete helm tiller from k8s cluster</strong>

Sometime, tiller is not working properly in k8s cluster and we want to delete evething Tiller. Tiller has 1 Deployment, 1 ReplicasSet and 1 Pod. 

To uninstall tiller from a kubernetes cluster

<pre><code data-trim class="yaml">
helm reset
</code></pre>

To delete failed tiller from a kubernetes cluster

<pre><code data-trim class="yaml">
helm reset --force
</code></pre>
