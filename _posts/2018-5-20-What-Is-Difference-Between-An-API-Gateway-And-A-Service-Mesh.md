---
layout: post
section-type: post
title: What Is Difference Between An API Gateway And A Service Mesh?
category: devops
tags: [ 'devops', 'kubernetes', 'apigateway', 'servicemesh', 'microservices', 'docker', 'kubenetes']
--- 

<strong>What is API Gateway?</strong>

An API Gateway is a server that is the single entry point into the system. It is similar to the Facade pattern from object-oriented design. The API Gateway encapsulates the internal system architecture and provides an API that is tailored to each client. It might have other responsibilities such as authentication, monitoring, load balancing, caching, request shaping and management, and static responce handling.

The API Gateway means that you put an API gateway in front of your microservices and make the API Gateway become the entry point for every new request that's being executed by the app. This can simplify significantly both the client implementations and the microservices app.

![apigateway architecture]({{ site.baseurl | cdn }}/img/post/apigateway02.png){:class="img-responsive"}

The API Gateway is responsible for request routing, composition, and protocol translation. All requests from clients first go through the API Gateway. It then routes requests to the appropriate microservice. The API Gateway will often handle a request by invoking multiple microservices and aggregating the results. It can translate between web protocols such as HTTP and WebSocket and web-unfriendly protocols that are used internally. 

An example of an API Gateway is Kong. See the Kong architecture below.

![apigateway architecture]({{ site.baseurl | cdn }}/img/post/apigateway03.png){:class="img-responsive"}

So why do we need an API Gateway, and how can an API gateway help in a microservice-oriented architecture? We always hear the word orchestration, it shows an orchestra with the individuals (who are the microservices) playing their own instrument. Then we have the director (who is the API Gateway) who can somehow orchestrate how the requests are being processed by our architecture.

![What is API gateway]({{ site.baseurl | cdn }}/img/post/apigateway01.png){:class="img-responsive"}

<strong>What is a Service Mesh?</strong>

A Service mesh is a new paradigm that provides containers and microservcies based applications with services that integrated directly from within the computer cluster. A service mesh provides monitoring, scalability, and high availability services through APIs instead of using discrete appliances. This flexible framework removes the operational complexity associated with modern application. 

![What is service mesh?]({{ site.baseurl | cdn }}/img/post/servicemesh01.png){:class="img-responsive"}

With a service mesh,

- A given microservices won't directly communicate with the other microservices.
- Rather all service-to-service communicatations will take places on-top of a software component called service mesh (or side-car proxy)
- Service Mesh provides the built-in support for some network functions such as resiliency, service discovery, etc...
- Therefore, service developers can focus more on the business logic while most of the work related to the network communication is offloaded to the service mesh.
- For instance, you don't need to worry about circuit breaking when your microservice call another service anymore. That's already comes as part of service mesh.
- Service mesh is language agnostic: Since the microservice to service mesh proxy communication is always on top to standard protocols such as HTTP1.x/2.x, gRPC, etc...you can write your microservice from any technology and they will still work with the service mesh.

![What is service mesh?]({{ site.baseurl | cdn }}/img/post/servicemesh02.png){:class="img-responsive"}

<strong>Functionalities of Service Mesh</strong>

As we have seen earlier, the service mesh offers a set of application network function while some (primitive) network functions are still implemented the microservices level itself. There is no hard and fast rule on what functionalities should be offered from a service mesh. There are the most common features offered from a service-mesh.
- <b>Resiliency for inter-service communications:</b> Circuit-breaking, retries and timeouts, fault injection, fault handling, load balancing and failover.
- <b>Service discovery:</b> Discovery of service endpoints through a dedicated service registry.
- <b>Routing:</b> Primitive routing capabilities, but no routing logics related to the business functionality of the service.
- <b>Observability:</b> Metrics, monitoring, distributed logging, distributed tracing.
- <b>Security:</b> Transport level security (TLS) and key management.
- <b>Access control:</b> simple blacklist and whitelist based access control.
- <b>Deployment:</b> Native support for containers. Docker and Kubernetes.
- <b>Interservice communication protocols:</b> HTTP1.x,HTTP2,gRPC


<strong>What is difference between an API gateway and a service mesh?</strong>

API Gateway facilitate API communications between a client and an application, and across microservices wihthin an application. Operating at layer 7 (HTTP or HTTPS), an API Gateway provides both internal and external communication services, along with value-added services such as authentication, rate limiting, transformations, logging and more.

Service Mesh is an emerging new technology focused on routing internal communications. Operating primarily at Layer 4 (TCP), a service mesh provides internal communication along with healthchecks, circuit breakers and other services.

Because API Gateways and Service Meshes operate at different layers in the network stack, each technology has different strenghts.

