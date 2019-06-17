---
layout: post
section-type: post
title: Delete K8S Namespace Stuck At Terminating State
category: devops
tags: [ 'devops', 'kubernetes', 'terminating', 'namespace']
--- 

<strong>Problem</strong>

Sometimes after deleting a namespace (for example <b>kiali-operator</b>), it sticks on "Terminating" state, and when you enter:

<pre><code data-trim class="yaml">
kubectl delete namespace kiali-operator
</code></pre>

You'll get

<pre><code data-trim class="yaml">
warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
Error from server (Conflict): Operation cannot be fulfilled on namespaces "kiali-operator": The system is ensuring all content is removed from this namespace.  Upon completion, this namespace will automatically be purged by the system.
</code></pre>

<strong>Solution</strong>

Let's get rid of <b>kiali-operator</b> namespace!

<pre><code data-trim class="yaml">
kubectl get ns kiali-operator -o json > kiali-operator.json
</code></pre>

Edit <b>kiali-operator.json</b> and remove <b>kubernetes</b> from <b>finalizers</b>.

<pre><code data-trim class="yaml">
{
    "apiVersion": "v1",
    "kind": "Namespace",
    "metadata": {
        "annotations": {
            "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Namespace\",\"metadata\":{\"annotations\":{},\"labels\":{\"app\":\"kiali-operator\",\"version\":\"v0.21.0\"},\"name\":\"kiali-operator\"}}\n"
        },
        "creationTimestamp": "2019-06-17T05:03:48Z",
        "deletionTimestamp": "2019-06-17T05:11:37Z",
        "labels": {
            "app": "kiali-operator",
            "version": "v0.21.0"
        },
        "name": "kiali-operator",
        "resourceVersion": "34541023",
        "selfLink": "/api/v1/namespaces/kiali-operator",
        "uid": "4a4997ec-90bd-11e9-a3be-0619e18c0adc"
    },
    "spec": {
        "finalizers": [
            "kubernetes" <-- remove it
        ]
    },
    "status": {
        "phase": "Terminating"
    }
}
</code></pre>

Using kubernetes proxy

<pre><code data-trim class="yaml">
kubernetes proxy
</code></pre>

Then run this command
<pre><code data-trim class="yaml">
curl -k -H "Content-Type: application/json" -X PUT --data-binary @kiali-operator.json http://127.0.0.1:8001/api/v1/namespaces/kiali-operator/finalize
</code></pre>

Now, check your namespaces

<pre><code data-trim class="yaml">
kubernetes get namespaces
</code></pre>

Namespace <b>kiali-operator</b> has been gone!