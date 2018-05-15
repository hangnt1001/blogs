---
layout: post
section-type: post
title: Delete Container Pods Stuck In State Unknown On Kubernetes
category: devops
tags: [ 'devops', 'kubernetes', 'gcp', 'pod','container']
--- 

<strong>Delete container pods stuck in state unknown on kubernetes</strong>

Sometime, Pods are stuck in "Unknown" state. Those pods can't deleted. Cluster got into this state when I have been summitting many pods at once.

eg:

<pre><code data-trim class="yaml">
NAME                                                              READY     STATUS              RESTARTS   AGE
asdmp-doctor-3477391423-97t2v                                     0/1       Unknown             0          14h
asdmp-doctor-3477391423-9trtw                                     0/1       Pending             0          18s
asdmp-doctor-3477391423-kz70k                                     0/1       Unknown             0          13h
asdmp-listen-1508374093-9z5m4                                     0/1       Pending             0          18s
asdmp-listen-1508374093-kv0pk                                     0/1       Unknown             0          14h
asdmp-listen-1508374093-qw7wx                                     0/1       Unknown             0          13h
asdmp-work-2968084484-2vc42                                       0/1       Pending             0          17s
asdmp-work-2968084484-fd2w1                                       0/1       Unknown             0          14h
asdmp-work-2968084484-krh6h                                       0/1       Unknown             0          13h
cache-proxy-3325881277-21z27                                      0/1       Terminating         8          6h
cache-proxy-3325881277-539zt                                      0/1       Unknown             0          13h
cache-proxy-3325881277-b489x                                      0/1       ContainerCreating   0          16s
cache-proxy-3325881277-jg1jm                                      0/1       Unknown             0          14h
</code></pre>

To list "unknown" pod, 

<pre><code data-trim class="yaml">
kubectl get pods -o wide | grep Unknown
</code></pre>

Use the below command to delete these pods.

<pre><code data-trim class="yaml">
kubectl delete pods <unknown pods name> --grace-period=0 --force
</code></pre>

