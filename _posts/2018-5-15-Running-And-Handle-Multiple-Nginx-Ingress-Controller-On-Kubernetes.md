---
layout: post
section-type: post
title: Running and Handle Multiple Nginx Ingress Controller on Kubernetes
category: devops
tags: [ 'devops', 'kubernetes', 'nginx', 'ingress','controller','multiple']
--- 

<strong>Running and handle multiple nginx ingress controller on kubernetes</strong>

In Kubernetes, Ingress allows extenal users and client applications access to HTTP services. Ingress consists of two components. The first component is Ingress Resource is a collection of rules for the inbound traffic to reach Services. These are Layer 7 (L7) rules that allow hostnames (and optionally paths) to be directed to specific Services in Kubernetes. The second component is the Ingress Controller which acts upon the rules set by the ingress Resource, typically via an HTTP or L7 load balancer. It is vital that poth pieces are properly configured to route traffic from an outside client to a Kubernetes Service.

Nginx is a popular choice for an Ingress Controller for a variety of features:

- Websocket, which allows you to load balance Websocket applications.
- SSL Services, which allows you to load balance HTTPS applications.
- Rewrites, which allows you to rewrite the URI of a request before sending it to the application.
- Session Persistence (Nginx Plus only), which guarantees that all the requests from the same client are always passed to the same backend container.
- Support for JWTs (Nginx Plus only), which allows Nginx Plus to authenticate requests by validating JSON Web Tokens (JWTs).

![ingress controller diagram]({{ site.baseurl | cdn }}/img/post/nginx-ingress-01.png){:class="img-responsive"}

So at this point, we have deployed an application. We can deploy another application as well and can access it using same ELB, but the only condition is that ELB DNS should be mapped to the differnece URL endpoint. But let's say we have one specific application that needs a separate ELB, so for that, we can launch another ingress controller. But to launch another ingress controller, we have to mention its class so that difference ingress of different should pick up their respective ingress controller. If you won't mention the ingress class of the second controller, then it's pod will go to CrashLoopBack state.

We need to deploy ingress controller with a specific class name and later in ingerss file we can give the annotation as ingress class.

For eg:

<pre><code data-trim class="yaml">
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-ingress-controller
spec:
  replicas: 1
  revisionHistoryLimit: 3
  template:
    metadata:
      labels:
        k8s-app: nginx-ingress-lb
    spec:
      containers:
        - args:
            - /nginx-ingress-controller
            - --default-backend-service=$(POD_NAMESPACE)/default-http-backend
            - --default-ssl-certificate=$(POD_NAMESPACE)/tls-flexdigitalhealth
            - --election-id=ingress-controller-leader
            - --ingress-class=nginx-custom
            - --configmap=$(POD_NAMESPACE)/nginx-ingress-controller
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
          image: "us.gcr.io/k8s-ingress-controller/nginx-ingress-controller:0.9.0"
          imagePullPolicy: Always
          livenessProbe:
            httpGet:
              path: /healthz
              port: 10254
              scheme: HTTP
            initialDelaySeconds: 10
            timeoutSeconds: 5
          name: nginx-ingress-controller
          resources:
            limits:
              cpu: "2"
            requests:
              cpu: "1"
          ports:
            - containerPort: 80
              name: http
              protocol: TCP
            - containerPort: 443
              name: https
              protocol: TCP
          volumeMounts:
            - mountPath: /etc/nginx-ssl/dhparam
              name: tls-dhparam-vol
      terminationGracePeriodSeconds: 60
      volumes:
        - name: tls-dhparam-vol
          secret:
            secretName: tls-dhparam
</code></pre>

In the above deployment file of ingress controller, we have passed an argument as <bold>--ingress-class=nginx-custom</bold>

So this ingress controller will belong to class nginx-custom.

Now if we have to create another ingress controller the we have to give another class name means other than nginx-custom.

Now to assign an ingress to a specific ingress controller then you have to pass the ingress class like below:

<pre><code data-trim class="yaml">
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: custom-nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/enable-cors: "true"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /
    kubernetes.io/ingress.class: "nginx-custom"
    kubernetes.io/tls-acme: "true"
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "route"
    nginx.ingress.kubernetes.io/session-cookie-hash: "sha1"
spec:
  rules:
  - host: abc.gluesolution.xyz
    http:
      paths:
      - path: /abc
        backend:
          serviceName: abc-service
          servicePort: 80
</code></pre>

So in this way, you can run multiple ingress controller and assign the ingress to them.
