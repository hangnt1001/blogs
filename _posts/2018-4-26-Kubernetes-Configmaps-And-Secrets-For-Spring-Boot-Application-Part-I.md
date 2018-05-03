---
layout: post
section-type: post
title: Kubernetes Configmaps And Secrets For Spring Boot Application Part I
category: devops
tags: [ 'devops', 'kubernetes', 'gcp', 'springboot','configmaps','secrets']
--- 

<strong>Kubernetes configmaps and secrets for spring boot application part I</strong>

Everyone needs to configure applications. You often need to reference “special” bits of data, such as API keys, tokens, and other secrets. Your app may be tunable using configuration settings, for example, a PHP.ini file, or environment variables and flags that change logic in your app.

You may be tempted to hard code these references into your application logic. In a small, standalone application, this might be acceptable, but it quickly becomes unmanageable in any reasonably sized app.

Of course, we have solved this problem. You can use environment variables and configuration files to store this information in a “central location” and reference these from our app. When you want to change the configuration, simply change it in the file or change the environment variable, and you are good to go! No need to hunt down every location where the data is referenced.

This problem becomes exacerbated in the land of containers and microservices. Docker lets you specify environment variables in the Dockerfile, but what if you need to reference the same data in two different containers? If you try to use the host environment, what happens when you are running on a cluster with multiple machines?

Let’s take an app with hardcoded configuration, change it to read from environment variables, and finally set up Kubernetes to manage the configuration for you.

<strong>The Hardcoded App</strong>

Here is an example.
![the hardcoed app]({{ site.baseurl | cdn }}/img/post/configmaps1.png){:class="img-responsive"}
If you wanted to change the language or the API key, you would need to touch the code. This can lead to bugs, security leaks, and pollutes the source history.

Let’s move to using environment variables instead.

<strong>Step 1: Using environment variables</strong>

Moving to environment variables is easy. Most programming languages have a built in way to read them.
![environment variables]({{ site.baseurl | cdn }}/img/post/configmaps2.png){:class="img-responsive"}

That’s all it takes!

Now we can set these environment variables and don’t have to touch our application’s code

In a Unix system (like MacOS and Linux), you can run this command to set environment variables for your current session:

<pre><code data-trim class="yaml">
export LANGUAGE=English
export API_KEY=123–456–789
</code></pre>

For Windows, you can use this command:

<pre><code data-trim class="yaml">
setx LANGUAGE “English”
setx API_KEY “123–456–789”
</code></pre>

You can set environment variables when starting the server as well.

For example, for this Node.js app, you can run:

<pre><code data-trim class="yaml">
LANGUAGE=Spanish API_KEY=0987654 npm start
</code></pre>

And these will be exposed to the app.

<strong>Step 2: Moving to Docker Environment Variables</strong>

When you move to a containerized solution, you can no longer depend on the host’s environment variables. Each container has its own environment, so it is important to make sure the container’s environment is properly configured. Thankfully, Docker makes it easy to build containers with environment variables baked in. In your Dockerfile, you can specify them with the ENV directive.

![docker environment variables]({{ site.baseurl | cdn }}/img/post/configmaps3.png){:class="img-responsive"}

Put the Dockerfile in the same directory as your code.

Then build the container:
<pre><code data-trim class="yaml">
docker build -t envtest .
</code></pre>

After building the container, run it with the following command:
<pre><code data-trim class="yaml">
docker run -p 3000:3000 -ti envtest
</code></pre>

You can also override the environment variables when running on the command line:
<pre><code data-trim class="yaml">
docker run -e LANGUAGE=Spanish -e API_KEY=09876 -p 3000:3000 \
           -ti envtest
</code></pre>

<strong>Step 3: Moving to Kubernetes Environment Variables</strong>

When moving from Docker to Kubernetes, things change once again. You might use the same Docker container in multiple kubernetes deployments or you might want to do A/B testing with your deployments where you use the same container with different configurations.

Just like the Dockerfile, you can specify environment variables directly in your Kubernetes Deployment YAML file. This means each deployment can get a customized environment.

![kubernetes environment variables]({{ site.baseurl | cdn }}/img/post/configmaps4.png){:class="img-responsive"}

The important section is the env portion of the pod spec. You can specify as many environment variables as you want here. Both environment variables are now set through Kubernetes!

<strong>Step 4: Centralized Configuration with Kubernetes Secrets And Configmaps</strong>

The shortfall of Docker and Kubernetes environment variables is that they are tied to the container or deployment. If you want to change them, you have to rebuild the container or modify the deployment. Even worse, if you want to use the variable with multiple containers or deployments, you have to duplicate the data!

Thankfully, Kubernetes solves this problem with Secrets (for confidential data) and ConfigMaps (for non-confidential data).

The big difference between Secrets and ConfigMaps are that Secrets are obfuscated with a Base64 encoding. There may be more differences in the future, but it is good practice to use Secrets for confidential data (like API keys) and ConfigMaps for non-confidential data (like port numbers).

Let’s save the API key as a Secret:

<pre><code data-trim class="yaml">
kubectl create secret generic apikey --from-literal=API_KEY=123–456
</code></pre>

And the language as a ConfigMap:
<pre><code data-trim class="yaml">
kubectl create configmap language --from-literal=LANGUAGE=English
</code></pre>

You can check that these are created with the following commands:

<pre><code data-trim class="yaml">
kubectl get secret
</code></pre>

![get secrets]({{ site.baseurl | cdn }}/img/post/configmaps5.png){:class="img-responsive"}

And
<pre><code data-trim class="yaml">
kubectl get configmap
</code></pre>

![get configmap]({{ site.baseurl | cdn }}/img/post/configmaps6.png){:class="img-responsive"}

Now, you can modify your Kubernetes deployment to read from these instead of the hardcoded values.

![final kubernetes deployment]({{ site.baseurl | cdn }}/img/post/configmaps7.png){:class="img-responsive"}

<strong>Updating Secrets And Configmaps</strong>

As mentioned above, having Kubernetes manage your environment variables for you means you don’t have to change code or rebuild containers when you want to change the value of the variable.

Because pods cache the value of environment variables during startup, this is a two step process.

First, update the values:

<pre><code data-trim class="yaml">
kubectl create configmap language --from-literal=LANGUAGE=Spanish \
        -o yaml --dry-run | kubectl replace -f -
kubectl create secret generic apikey --from-literal=API_KEY=098765 \
        -o yaml --dry-run | kubectl replace -f -
</code></pre>

Then, restart the pods. This can be done is a variety of ways, such as forcing a new deployment. The quick and easy way is to delete the pods manually and have the deployment automatically spin up new ones.

<pre><code data-trim class="yaml">
kubectl delete pod -l name=envtest
</code></pre>

That’s all it takes! The new values will now be used by your app!

In <a href="https://gluesolution.xyz/devops/2018/04/27/Kubernetes-Configmaps-And-Secrets-For-Spring-Boot-Application-Part-II.html">Part II</a>, We'll go to use configuration files, which are even more powerful in Kubernetes than Environment Variables.

