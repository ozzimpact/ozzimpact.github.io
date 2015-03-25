---
layout: post
title: "Jenkins, OpenCover, NUnit, Code Metrics, Coberture Oh my!"
description: Jenkins, OpenCover, NUnit, Code Metrics, Coberture Oh my!
headline: 
category: engineering
tags: [jenkins, opencover, code, metrics, coberture, ci, git]
comments: true
mathjax: 
---

## Jenkins, OpenCover, NUnit, Code Metrics, Coberture Oh my!

###Requirements  
**Jenkins**  
Please use instructions from here [Installing Jenkins on Windows](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins)  
**Git**  
Download and install ``git`` from [link](https://msysgit.github.io/)  
Jenkins Plugins;  
- Install Git Plugin  
- Install MsBuildPlugin Pugin  
- Install HtmlPublisherPlugin  
- Install NUnitPlugin  
- Install Visual Studio Code Metrics Plugin  
- Install Cobertura Plugin  

###Prepare Environment;  
**Register MsBuild**  
TODO:  
**Register Visual Studio Code Metrics Tool**  
TODO:  

{% highlight bash %}
"C:\Tools\opencover\OpenCover.Console.exe" -target:"C:\Tools\nunit\nunit-console.exe" -targetargs:"%JOB_NAME%.Tests\bin\Debug\%JOB_NAME%.Tests.dll /framework:net-4.5 /xml:%JOB_NAME%NunitTestResults.xml /nologo /noshadow" -filter:"+[*]* -[%JOB_NAME%.Tests]*" -register:Path64 -hideskipped:Filter -output:%JOB_NAME%Coverage.xml
{% endhighlight %}
