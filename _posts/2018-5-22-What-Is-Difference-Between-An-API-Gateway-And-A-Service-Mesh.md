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

As we have seen earlier, the service mesh offers a set of application network function while some (primitive) network functions are still implemented the microservices level itself. There is no hard and fast rule on what functionalities should be offered from a service mesh. Service mesh comes with its own terminology for component services and functions:
- <b>Container orchestration framework:</b> As more and more containers are added to an application’s infrastructure, a separate tool for monitoring and managing the set of containers – a container orchestration framework – becomes essential. Kubernetes seems to have cornered this market, with even its main competitors, Docker Swarm and Mesosphere DC/OS, offering integration with Kubernetes as an alternative.
- <b>Services vs. service instances:</b> To be precise, what developers create is not a service, but a service definition or template for service instances. The app creates service instances from these, and the instances do the actual work. However, the term service is often used for both the instance definitions and the instances themselves.
- <b>Sidecar proxy:</b> A sidecar proxy is a proxy instance that’s dedicated to a specific service instance. It communicates with other sidecar proxies and is managed by the orchestration framework.
- <b>Service discovery:</b> When an instance needs to interact with a different service, it needs to find – discover – a healthy, available instance of the other service. The container management framework keeps a list of instances that are ready to receive requests.
- <b>Load balancing:</b> In a service mesh, load balancing works from the bottom up. The list of available instances maintained by the service mesh is stack‑ranked to put the least busy instances – that’s the load balancing part – at the top.
- <b>Encryption:</b> The service mesh can encrypt and decrypt requests and responses, removing that burden from each of the services. The service mesh can also improve performance by prioritizing the reuse of existing, persistent connections, reducing the need for the computationally expensive creation of new ones.
- <b>Authentication and authorization:</b> The service mesh can authorize and authenticate requests made from both outside and within the app, sending only validated requests to service instances
- <b>Deployment:</b> Native support for containers. Docker and Kubernetes.
- <b>Support for the circuit breaker patterns:</b> The service mesh can support the circuit breaker pattern, which isolates unhealthy instances, then gradually brings them back into the healthy instance pool if warranted.

The part of the service mesh where the work is getting done – service instances, sidecar proxies, and the interaction between them – is called the data plane of a service mesh application. (Though it’s not included in the name, the data plane handles processing too.) But a service mesh application also includes a monitoring and management layer, called the control plane.

The control plane handles tasks such as creating new instances, terminating unhealthy or unneeded instances, monitoring, integrating monitoring and management, implementing application‑wide policies, and gracefully shutting down the app as a whole. The control plane typically includes, or is designed to connect to, an application programming interface, a command‑line interface, and a graphical user interface for managing the app.

![What is service mesh?]({{ site.baseurl | cdn }}/img/post/servicemesh03.png){:class="img-responsive"}

<strong>What is difference between an API gateway and a service mesh?</strong>

API Gateway facilitate API communications between a client and an application, and across microservices wihthin an application. Operating at layer 7 (HTTP or HTTPS), an API Gateway provides both internal and external communication services, along with value-added services such as authentication, rate limiting, transformations, logging and more.

Service Mesh is an emerging new technology focused on routing internal communications. Operating primarily at Layer 4 (TCP), a service mesh provides internal communication along with healthchecks, circuit breakers and other services.

Because API Gateways and Service Meshes operate at different layers in the network stack, each technology has different strenghts.

