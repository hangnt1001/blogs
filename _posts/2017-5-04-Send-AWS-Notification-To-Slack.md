---
layout: post
section-type: post
title: Send AWS Notification to Slack
category: sysadmin
tags: [ 'sysadmin', 'devops', 'aws', 'slack', 'lambda', 'SNS', 'S3', 'SES' ]
--- 

In this post, we are going to ingrage AWS Lambda with slack to receive importance AWS notification in the slack channel.

<strong>Create an incomming webhook in slack</strong>

- Step 1: Go to your slack application then click on your team name
![Slack Notification]({{ site.baseurl | cdn }}/img/post/slack01.png){:class="img-responsive"}
- Step 2: You will find a popup menu and click on App and Custom Integrations
![Slack Notification]({{ site.baseurl | cdn }}/img/post/slack02.png){:class="img-responsive"}
- Step 3: You will find the application site of Slack. Type "incomming" in the search box and select Incomming Webhooks
![Slack Notification]({{ site.baseurl | cdn }}/img/post/slack03.png){:class="img-responsive"}
- Step 4: Click add configuration
![Slack Notification]({{ site.baseurl | cdn }}/img/post/slack04.png){:class="img-responsive"} 
- Step 5: Post to channel or create new channel in slack then click add incomming webhooks integration
![Slack Notification]({{ site.baseurl | cdn }}/img/post/slack05.png){:class="img-responsive"}
- Step 6: We also can customize name, icon...and all we need is a Webhook URL which will use in Lambda function
![Slack Notification]({{ site.baseurl | cdn }}/img/post/slack06.png){:class="img-responsive"}

<strong>Create a SNS topic</strong>

- Step 1: Go to the SNS console and create a new topic
![Slack Notification]({{ site.baseurl | cdn }}/img/post/sns01.png){:class="img-responsive"}
- Step 2: Insert name and display name
![Slack Notification]({{ site.baseurl | cdn }}/img/post/sns02.png){:class="img-responsive"}

<strong>Create a lambda function which sends the notification to the slack</strong>

- Step 1: Go to AWS lambda console and create a lambda function
![AWS lambda]({{ site.baseurl | cdn }}/img/post/lambda01.png){:class="img-responsive"}
- Step 2: Choose blank function
![AWS lambda]({{ site.baseurl | cdn }}/img/post/lambda02.png){:class="img-responsive"}
- Step 3: Configure triggers, choose SNS service and select SNS topic which we created above
![AWS lambda]({{ site.baseurl | cdn }}/img/post/lambda03.png){:class="img-responsive"}
- Step 4: Insert name, description for your lambda function. Select Node.js 6.10 in Runtime
![AWS lambda]({{ site.baseurl | cdn }}/img/post/lambda04.png){:class="img-responsive"}
- Step 5: Paste the below code into a lambda function code
<pre><code data-trim class="yaml">
var https = require('https');
var util = require('util');

var CHANNEL = "#your channel";
var PATH = "/services/your web hook";

exports.handler = function(event, context) {
    console.log(JSON.stringify(event, null, 2));
    console.log('From SNS:', event.Records[0].Sns.Message);

    var postData = {
        "channel": CHANNEL,
        "username": "AWS Notification on behalf of Harry",
        "text": "*" + event.Records[0].Sns.Subject + "*",
        "icon_emoji": ":bomb:"
    };

    var message = event.Records[0].Sns.Message;
    var severity = "good";

    var dangerMessages = [
        " but with errors",
        " to RED",
        "During an aborted deployment",
        "Failed to deploy application",
        "Failed to deploy configuration",
        "has a dependent object",
        "is not authorized to perform",
        "Pending to Degraded",
        "Stack deletion failed",
        "Unsuccessful command execution",
        "You do not have permission",
        "Your quota allows for 0 more running instance"];

    var warningMessages = [
        " aborted operation.",
        " to YELLOW",
        "Adding instance ",
        "Degraded to Info",
        "Deleting SNS topic",
        "is currently running under desired capacity",
        "Ok to Info",
        "Ok to Warning",
        "Pending Initialization",
        "Removed instance ",
        "Rollback of environment"        
        ];
    
    for(var dangerMessagesItem in dangerMessages) {
        if (message.indexOf(dangerMessages[dangerMessagesItem]) != -1) {
            severity = "danger";
            break;
        }
    }
    
    // Only check for warning messages if necessary
    if (severity == "good") {
        for(var warningMessagesItem in warningMessages) {
            if (message.indexOf(warningMessages[warningMessagesItem]) != -1) {
                severity = "warning";
                break;
            }
        }        
    }

    postData.attachments = [
        {
            "color": severity, 
            "text": message
        }
    ];

    var options = {
        method: 'POST',
        hostname: 'hooks.slack.com',
        port: 443,
        path: PATH
    };

    var req = https.request(options, function(res) {
      res.setEncoding('utf8');
      res.on('data', function (chunk) {
        context.done(null, postData);
      });
    });
    
    req.on('error', function(e) {
      console.log('problem with request: ' + e.message);
    });    

    req.write(util.format("%j", postData));
    req.end();
};
</code></pre>
- Step 6: leave <strong>Handler</strong> set to index.handler. Select <strong>Basic Execution Role</strong> from the role list and select lambda_basic_execution. After creating the lambda function, you should be taken back to the lambda function list screen.
![AWS lambda]({{ site.baseurl | cdn }}/img/post/lambda05.png){:class="img-responsive"}

<strong>Test Lambda Function</strong>
On the lambda function list screen, click on your function and choose edit or test function. Below is an example notification
![AWS lambda]({{ site.baseurl | cdn }}/img/post/lambda06.png){:class="img-responsive"}

Cheers
