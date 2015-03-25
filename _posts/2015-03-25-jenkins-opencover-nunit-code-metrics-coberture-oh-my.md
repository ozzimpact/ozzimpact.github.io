---
layout: post
title: "Jenkins Tutorial"
description: Jenkins, OpenCover, NUnit, Code Metrics, Cobertura Oh my!
headline: 
category: engineering
tags: [jenkins, opencover, code, metrics, cobertura, ci, git, continuous integration]
comments: true
mathjax: 
---

## Jenkins, OpenCover, NUnit, Code Metrics, Cobertura Oh my!

Requirements  
---------------------------------------------------------
**Jenkins**  
Please use instructions from here [Installing Jenkins on Windows](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins)  
**Git**  
Download and install ``git`` from [link](https://msysgit.github.io/)  

**Jenkins Plugins**  
- [Git](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin)  
- [MsBuildPlugin](https://wiki.jenkins-ci.org/display/JENKINS/MSBuild+Plugin)  
- [HtmlPublisherPlugin](https://wiki.jenkins-ci.org/display/JENKINS/HTML+Publisher+Plugin)   
- [NUnitPlugin](https://wiki.jenkins-ci.org/display/JENKINS/NUnit+Plugin)    
- [Visual Studio Code Metrics](https://wiki.jenkins-ci.org/display/JENKINS/Visual+Studio+Code+Metrics+Plugin)    
- [Cobertura](https://wiki.jenkins-ci.org/display/JENKINS/Cobertura+Plugin)  

**Tools**  
- [ReportGenerator](https://github.com/danielpalme/ReportGenerator)  
- [OpenCover](https://github.com/OpenCover/opencove)  
- [Metrics Power Tools](http://www.microsoft.com/en-us/download/confirmation.aspx?id=41647)  
- [OpenCoverToCoberturaConverter](http://www.nuget.org/packages/OpenCoverToCoberturaConverter)  

_**Info:** Extract all tools to ``C:\Tools\{toolname}\`` folder._

Jenkins Settings  
---------------------------------------------------------
**Register MsBuild** 
{% highlight bash %}
C:\Program Files (x86)\MSBuild\12.0\Bin\MSBuild.exe
{% endhighlight %}
![Jenkins MSBuild Settings]({{ site.url }}/images/post/2015-03-20/2015-03-25_20-39-27.png)  

**Register Visual Studio Code Metrics Tool**   
{% highlight bash %}
C:\Program Files (x86)\Microsoft Visual Studio 12.0\Team Tools\Static Analysis Tools\FxCop\metrics.exe
{% endhighlight %}
![Jenkins Visual Studio Code Metrics Tool Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-22-30.png)  

Create Jenkins Build Job  
---------------------------------------------------------
**Source Code Management settings**  
Configure ``Jenkins`` job to get source code from ``git`` repository.
![Jenkins Job Source Control Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-21-27.png)
