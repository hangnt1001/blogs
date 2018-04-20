---
layout: post
section-type: post
title: Compare AWS CloudFormation And Terraform
category: devops
tags: [ 'devops', 'aws', 'cloudformation', 'terraform']
--- 

<strong>CloudFormation VS Terraform</strong>

The most reliable way to automate creating, updating and deleting your cloud resources is to describe the target state of your infrastructure and use a tool to apply it to the current state of your infrastructure. CloudFormation and Terraform are the most valuable tools to implement Infrastructure as Code on AWS.

I have worked with CloudFormation and Terraform in various projects. You will learn about the differences between CloudFormation and Terraform during this article.

Before we start, both tools are following a very similar approach.

- You define a template (CloudFormation) or configuration (Terraform) describing the target state of your infrastructure.
- The tool (CloudFormation or Terraform) calculates the necessary steps to reach the defined target.
- The tool (CloudFormation or Terraform) executes the changes.
But what are the differences of both tools?

<strong>Scope</strong>

CloudFormation covers almost all bits and parts of AWS. Terraform covers the most important AWS resources as well. But on top of that Terraform can provision infrastructure at other cloud providers as well as 3rd party services. A shortened list of vendors supported by Terraform: Bitbucket, Datadog, DigitalOcean, Fastly, GitHubm, Gitlab, Google Cloud, Heroku, OpenStack, Mailgun, Microsoft Azure, and New Relic.

![Scope]({{ site.baseurl | cdn }}/img/post/cf-tf-scope.png){:class="img-responsive"}
Depending on your infrastructure a big or at least small plus for Terraform.

<strong>Licence and Support</strong>

CloudFormation is a service offered by AWS for free. The AWS support plans include support for CloudFormation.

Terraform is an Open Source project. Hashicorp, the company behind Terraform, is offering support plans as well.

![License]({{ site.baseurl | cdn }}/img/post/cf-tf-licence.png){:class="img-responsive"}

When already subscribed to an AWS support plan, that might be a plus for CloudFormation. If you prefer Open Source that is a plus for Terraform.

<strong>State Management</strong>

Both tools need to keep track of all the resources under management. CloudFormation is managing its state with so called stacks. By default, Terraform is storing its state on disk. Terraform is offering remote state as well, for example, based on S3 and DynamoDB. It is advisable to use remote state when multiple users are working on the same infrastructure in parallel.

![State]({{ site.baseurl | cdn }}/img/post/cf-tf-state.png){:class="img-responsive"}

CloudFormation manages state within the managed service out-of-the-box which is a small plus compared to Terraform where you need to configure remote state yourself.

<strong>Modularization</strong>

An infrastructure for a typical web application consists of a lot of resources: VPC, Subnets, Security Groups, Auto Scaling Group, Elastic Load Balancer, to name a few. Specifying all these resources in a single blueprint will cause you headaches when maintaining the system in the future. Using small modules that you stick together as needed is a common approach.

Both CloudFormation and Terraform do support modularizing your infrastructure blueprints.
![Modularization]({{ site.baseurl | cdn }}/img/post/cf-tf-modularization.png){:class="img-responsive"}
Handling modules with Terraform is simple. CloudFormation is offering multiple ways to create modules with different pros and cons. I’d award Terraform with a plus for usability.

<strong>Verify Changes</strong>

CloudFormation and Terraform do not only allow you to create your infrastructure from scratch automatically. You can use both tools to update your infrastructure later as well. When applying a change to your infrastructure based on changes you made to your blueprint it is soothing to be able to verify the changes the tool will execute on your infrastructure.

CloudFormation offers change sets that you can use to verify changes. Terraform provides a command named plan which gives you a very detailed overview of what will be modified if you apply your blueprint.
![Verify]({{ site.baseurl | cdn }}/img/post/cf-tf-verify-changes.png){:class="img-responsive"}

Terraform presents a detailed and readable summary of the changes that will be applied. That’s a big plus compared to the basic overview CloudFormation is providing with a change set.

<strong>Wait Conditions</strong>

It is useful to be able to add wait conditions to your infrastructure automation from time to time. For example, if you want to wait until a service has been started on a virtual machine. Using wait conditions allows you to wait for signals sent via HTTPS before depended resources are created or updated when using CloudFormation.
![wait conditions]({{ site.baseurl | cdn }}/img/post/cf-tf-wait-conditions.png){:class="img-responsive"}

Being able to use wait conditions is a plus for CloudFormation. Terraform does not support wait conditions.

<strong>Rolling Update</strong>

What happens when you change a Launch Configuration of an Auto Scaling Group within your blueprint? When using an update policy, CloudFormation will perform a rolling update including a rollback in case of a failure. Terraform does not support rolling updates for Auto Scaling Groups out-of-the-box.
![Rolling Update]({{ site.baseurl | cdn }}/img/post/cf-tf-rolling-update.png){:class="img-responsive"}

Supporting rolling updates for Auto Scaling Groups is a plus for CloudFormation.

<strong>Handle Existing Resources</strong>

Besides using input parameters, there’s no way to access existing resources with CloudFormation. It is also not possible to import an existing resource into a CloudFormation stack. Terraform allows you to import existing resources. On top of that data, providers enable you to query attributes from existing resources.

![Handle Existing Resources]({{ site.baseurl | cdn }}/img/post/cf-tf-exsisting-resources.png){:class="img-responsive"}

Handling existing resources is a plus for Terraform.

<strong>Summary</strong>

CloudFormation and Terraform are both powerful and mature tools. Going through the differences listed above will help you to make a decision for CloudFormation or Terraform depending on your use case. Personally, I prefer CloudFormation when the evaluation based on the differences of the tools does not produce a clear winner.

<pre><code data-trim class="yaml">
source: collection from internet
</code></pre>

