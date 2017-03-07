---
layout: post
section-type: post
title: Continuos Integration with Jenkins to build iOS .IPA
category: devops
tags: [ 'sysadmin', 'jenkins','ci','IOS','ipa' ]
--- 

In this post, I'll show you how Jenkins can be used to automate continuos integration for IOS application to build IOS .ipa.
<br/>
<strong>Requirements</strong>

- Xcode
- Jenkins
- MacOS

From my structure, I am using one Jenkins master installed on another Linux server and use Jenkins slave on MacOS. After configuring Jenkins master and slave, We add the following plugins to Jenkins master. (Jenkins slave will use the recommended plugins).

- Git
- Xcode Integration
- Hookeyapp (Used to manage version of .Ipa)
- Slack (Used to notify event)

<strong>Setup a new Jenkins item</strong>
<br/>
Create a new Jenkins project using freestyle option and the following settings.
- Give your project name
- Select Git, paste the right repository and select the right credentials.
- Make sure the right branche is selected
![Jenkins IOS]({{ site.baseurl | cdn }}/img/post/jenkinsipa1.JPG){:class="img-responsive"}
- Additional, add Job weight number to define the number of processor(s) used to build. Add the lable of MacOS slave used to build IOS application at Retrict where this project can be run.

 ![Jenkins IOS]({{ site.baseurl | cdn }}/img/post/jenkinsipa2.JPG){:class="img-responsive"}

- If we want to schedule strigger, select build periodically.

- To build IOS .ipa, we need three build steps:

1. Excute shell for running pod install or independent packages

<pre><code data-trim class="yaml">
cd ${WORKSPACE}
/usr/local/bin/pod install
</code></pre>

2. Xcode build

- To use xcode, we should know more about team provisioning profiles

![Jenkins IOS]({{ site.baseurl | cdn }}/img/post/teamprovisioningprofile_2x.png){:class="img-responsive"}

Link ref: <a href="https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppStoreDistributionTutorial/CreatingYourTeamProvisioningProfile/CreatingYourTeamProvisioningProfile.html">Link</a>

- If everything is fine until this point, you can go on and add an Xcode build step. This plugin will invoke the xcodebuild command line tool and you can add all the build parameters here.
- In this case:
+	Target: yourProjectName
+	Clean before build? Yes
+	Generate Archive? Yes
+	Pack application and build .ipa? Yes
+	.ipa file name pattern: ${VERSION}
+	Output directory:  ${BUILD_NUMBER}/${BUILD_ID}
+	Unlock keychain? Yes
+	Keychain path: ${HOME}/Library/Keychains/login.keychain
+	Keychain password: your administrator user password
+	Xcode Schema File: yourProjectSchema
+	Custom xcodebuild arguments: -sdk iphoneos  -archivePath yourPath
+	Xcode Workspace File: ${WORKSPACE}/yourProjectName
+	Xcode Project Directory: ${WORKSPACE}
+	Xcode Project File: ${WORKSPACE}/yourProjectName
+	Build output directory: ${WORKSPACE}/DerivedData
+	Provide version number and run avgtool? YES
+	Technical version: ${BUILD_ID}

![Jenkins IOS]({{ site.baseurl | cdn }}/img/post/jenkinsipa3.JPG){:class="img-responsive"}

3. Export IOS .ipa
- Create file options.plist with following content

![Jenkins IOS]({{ site.baseurl | cdn }}/img/post/jenkinsipa4.JPG){:class="img-responsive"}

- Excute shell to export IOS .ipa
<pre><code data-trim class="yaml">
cd ${WORKSPACE}/build
xcodebuild -exportArchive -archivePath  YourXcarchive  -exportPath  YourPath  -exportOptionsPlist  options.plist
</code></pre>

- After building the IOS .ipa, we can upload to hockeyApp or send to the FTP server then QA can use for testing. We also can use slack plugin to notify the event for the job.

![Jenkins IOS]({{ site.baseurl | cdn }}/img/post/jenkinsipa5.JPG){:class="img-responsive"}