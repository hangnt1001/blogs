---
layout: post
section-type: post
title: Kubernetes Configmaps And Secrets For Spring Boot Application Part III
category: devops
tags: [ 'devops', 'kubernetes', 'gcp', 'springboot','configmaps','secrets']
--- 

<strong>Kubernetes configmaps and secrets for spring boot application part III</strong>

This is a continuation from <a href="https://gluesolution.xyz/devops/2018/04/26/Kubernetes-Configmaps-And-Secrets-For-Spring-Boot-Application-Part-I.html">Part I</a> and <a href="https://gluesolution.xyz/devops/2018/04/27/Kubernetes-Configmaps-And-Secrets-For-Spring-Boot-Application-Part-II.html">Part II</a>

<strong>Spring Boot Externalized Configuration</strong>

In spring boot, externalized configuration is the machanism that enables you to inject configuration values from external sources into java code. In your java code, injection is typically enabled by annotating with the @value annotation (to inject into a single field) or the @ConfigurationProperties annotation (to inject into multiple properties on a java bean class).

The configuration data can come from a wide variety of different sources (or property sources). In particular, configuration properties are often set in a project's application.properties file (or application.yaml file, if you prefer).

<strong>Kubernetes ConfigMap</strong>

A kubernetes configmap is a mechanism that can provide configuration data to a deployed application. A configmap object is typically defined in a YAML file, which is then uploaded to the kubernetes cluster, making the configuration data available to deployed applications.

<strong>kubernetes Secrets</strong>

A kubernetes secrets is a machanism for providing sensitive data (such as passwords, certificates, and so on) to deployed applications.

<strong>Spring cloud Kubernetes Plug-In</strong>

The Spring Cloud Kubernetes plug-in implements the integration between Kubernetes and Spring Boot. In principle, you could access the configuration data from a ConfigMap using the Kubernetes API. It is much more convenient, however, to integrate Kubernetes ConfigMap directly with the Spring Boot externalized configuration mechanism, so that Kubernetes ConfigMaps behave as an alternative property source for Spring Boot configuration. This is essentially what the Spring Cloud Kubernetes plug-in provides.

<strong>How to Enable Spring Boot with Kubernetes integration</strong>

In a typical Spring Boot Maven project, you can enable the integration by adding the following Maven dependency to your project’s POM file:

![enable spring boot with k8s integration]({{ site.baseurl | cdn }}/img/post/configmaps15.png){:class="img-responsive"}

To complete the integration, you need to add some annotations to your java source code, create a Kubernetes configmap, modify the properties to allow your application can read the configmap object.

on the application.properties, modify the properties like below:

<pre><code data-trim class="yaml">
spring.application.name=abc-service
</code></pre>

The default property source is the application's application.properties file, and this can optionally overridden by a configmap object (name abc-service for an example above) or a secret object.