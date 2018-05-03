---
layout: post
section-type: post
title: Kubernetes Configmaps And Secrets For Spring Boot Application Part II
category: devops
tags: [ 'devops', 'kubernetes', 'gcp', 'springboot','configmaps','secrets']
--- 

<strong>Kubernetes configmaps and secrets for spring boot application part II</strong>

This is a continuation from <a href="https://gluesolution.xyz/devops/2018/04/26/Kubernetes-Configmaps-And-Secrets-For-Spring-Boot-Application-Part-I.html">Part I</a>

While environment variables are great for small bits of information, sometimes you might have a lot of data that needs to be passed to your app. A common solution is to group this data into a file, and have your app read from this file.

Kubernetes let’s you mount ConfigMaps and Secrets as files. Unlike environment variables, if these files change the new files will be pushed to the running pods without needing a restart, so they are a lot more powerful. You can also map multiple files to a single ConfigMap or Secret, and mount them all at once as a directory!

<strong>Read from a file</strong>

Let's modify our code to read from files instead of environment variables.

First, create a two subdirectories called “config” and “secret”, and config.json and secret.json files with the data we used in part one:

<pre><code data-trim class="yaml">
mkdir config && mkdir secret
echo '{"LANGUAGE":"English"}' > ./config/config.json
echo '{"API_KEY":"123-456-789"}' > ./secret/secret.json
</code></pre>

Now, edit the code so it reads in these files instead of environment variables.

![environment variables]({{ site.baseurl | cdn }}/img/post/configmaps8.png){:class="img-responsive"}

Important: This code will re-read the file on every request. If you read the file once when the program starts, updates to the file will not be captured and you will need to restart the container to update the files. A common pattern that is more efficient that re-reading the file every time is to use a file watcher that will reload the file only when it changes.

<strong>Mount the files using docker volumes</strong>

The first step is to test everything works by using Docker volumes to simulate the ConfigMaps and Secrets.

Rebuild the container:

<pre><code data-trim class="yaml">
docker build -t envtest .
</code></pre>

After building the container, run it with the following command:

<pre><code data-trim class="yaml">
docker run -p 3000:3000 -ti \
  -v $(pwd)/secret/:/usr/src/app/secret/ \
  -v $(pwd)/config/:/usr/src/app/config/ \
  envtest
</code></pre>

This will run the Docker container, and mount the data folders into the container.

Note: the onbuild container that is used for the base image puts the code into the /usr/src/app directory. That is why the folders are mounted there.

If you visit localhost:3000 the container should be serving traffic.

![serving traffic]({{ site.baseurl | cdn }}/img/post/configmaps9.png){:class="img-responsive"}

Because the file is mounted into the container and the code re-reads the file on every request, you can change the files and see the change without restarting anything!

For example:

<pre><code data-trim class="yaml">
echo '{"LANGUAGE":"Spanish"}' > ./config/config.json
</code></pre>

![serving traffic]({{ site.baseurl | cdn }}/img/post/configmaps10.png){:class="img-responsive"}

<strong>Creating the ConfigMap and Secret</strong>

Create the Secret from the file

<pre><code data-trim class="yaml">
kubectl create secret generic my-secret \
  --from-file=./secret/secret.json
</code></pre>

Then create the ConfigMap from the other file

<pre><code data-trim class="yaml">
kubectl create configmap my-config --from-file=./config/config.json
</code></pre>

You can check that these are created with the following commands:

<pre><code data-trim class="yaml">
kubectl get secret
</code></pre>

![get secrets]({{ site.baseurl | cdn }}/img/post/configmaps11.png){:class="img-responsive"}

And

<pre><code data-trim class="yaml">
kubectl get configmap
</code></pre>

![get configmap]({{ site.baseurl | cdn }}/img/post/configmaps11a.png){:class="img-responsive"}

<strong>Using ConfigMaps and Secrets as files</strong>

The final step is using creating a deployment that will use the ConfigMap and Secret as a file instead of an environment variable

Note: Remember you need to update and push the Docker image in your registry to use the new code. You can do that with this command if you are using Google Cloud.

<pre><code data-trim class="yaml">
gcloud container builds submit --tag gcr.io/$(gcloud config list project --format=text | awk '{print $2}')/envtest:file .
</code></pre>

In your deployment YAML, you can use the ConfigMap and Secret as a volume. This will automatically mount them as a directory in your container, just like with Docker.

![deployemnt yaml]({{ site.baseurl | cdn }}/img/post/configmaps12.png){:class="img-responsive"}

Notice that the Secret and ConfigMap are mapped to volumes, and these volumes are used in the volumeMount section of the container spec.

<strong>Updating with no downtime!</strong>

Unlike <a href="https://gluesolution.xyz/devops/2018/04/26/Kubernetes-Configmaps-And-Secrets-For-Spring-Boot-Application-Part-I.html">Part 1’s</a> environment variables, volumes can be dynamically remounted into running containers. This means that new ConfigMap and Secret values will be available to the container without needed to restart the running processes.

For example, change the language to Klingon and update the ConfigMap.

<pre><code data-trim class="yaml">
echo '{"LANGUAGE":"Klingon"}' > ./config/config.json
kubectl create configmap my-config \
  --from-file=./config/config.json \
  -o yaml --dry-run | kubectl replace -f -
</code></pre>

In a few seconds (up to a minute depending on the cache) the new file will be pushed to the running containers automatically!

![updating]({{ site.baseurl | cdn }}/img/post/configmaps13.gif){:class="img-responsive"}

If you want to update the secret, you can follow the same process:

<pre><code data-trim class="yaml">
echo '{"API_KEY":"0987654321"}' > ./secret/secret.json
kubectl create secret generic my-secret \
  --from-file=./secret/secret.json \
  -o yaml --dry-run | kubectl replace -f -
</code></pre>

Note: These updates are eventually consistent. It is possible that some containers get the update before others, creating inconsistencies in your deployments. If this is an issue, do not use the auto-update feature. Instead, create a new ConfigMap or Secret, and update or create a new Deployment to use the new one.

Let's move to <a href="https://gluesolution.xyz/devops/2018/04/28/Kubernetes-Configmaps-And-Secrets-For-Spring-Boot-Application-Part-III.html">Part III</a> to configure a kubernetes configmap and secrets for spring boot application.
