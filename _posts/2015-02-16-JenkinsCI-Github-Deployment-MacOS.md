---
date: 2015-02-16
layout: post
title: Jenkins CI Deployment on MacOS
category: tech
tags: Jenkins
---
##Install Jenkins
**Install Jenkins**  
It's pretty easy to install Jenkins on MacOS.  
Just download [this pkg](http://mirrors.jenkins-ci.org/osx/latest) and follow the install wizard.  
Hit http://localhost:8080 and you will see the Jenkins Dashboard.

**Jenkins Config**  
Config path of JDK, Git and Maven.  
You can also refer this [vedio](https://www.youtube.com/watch?v=lTQGi5jzjvo).

**Install github plugin**  
Go to [pluginManager page](http://localhost:8080/pluginManager/) to install github plugin.
Choose Available tab and input 'github' as filter in the top-right corner.
Choose `Github Plugin`, download and install it.

Hit localhost:8080/restart to restart Jenkins.

##Config github Web Hook
Now we need to config github web hook to enable builder trigger.

**Jenkins config**  
In [Jenkins config](http://localhost:8080/configure), choose "Let Jenkins auto-manage" hook URLs and fill API URL, Username(optional) and [OAuth token](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).
![GithubWebHook.png](/images/process/GithubWebHook.png)

**Jenkins Project config**  
In your project config: http://localhost:8080/job/YOUR-PROJECT/configure
check the box:
![BuilderTriggers](/images/process/BuilderTriggers.png)

**Github Config**  
In github config: https://github.com/YOUR-NAME/REPO/settings/hooks, set the payload URL. If you are using local machine, first, we’ll install [ngrok](https://ngrok.com/download) to expose local host to the Internet.

You can expose your localhost by running ./ngrok http 8080 on the command line. You should see a line that looks something like this:
>Forwarding  http\://128cd298.ngrok.io -> localhost:8080 

![BuilderTriggers](/images/process/payloadURL.png)

Now pushes to your Github repository will automatically trigger builds in Jenkins.

