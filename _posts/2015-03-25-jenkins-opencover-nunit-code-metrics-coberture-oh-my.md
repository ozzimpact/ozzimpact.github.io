---
layout: post
title: "Jenkins, OpenCover, NUnit, Code Metrics, Coberture Oh my!"
description: Jenkins, OpenCover, NUnit, Code Metrics, Coberture Oh my!
headline: 
category: engineering
tags: [jenkins, opencover, code, metrics, cobertura, ci, git, continuous integration]
comments: true
mathjax: 
---

## Jenkins, OpenCover, NUnit, Code Metrics, Cobertura Oh my!

###Requirements  
**Jenkins**  
Please use instructions from here [Installing Jenkins on Windows](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins)  
**Git**  
Download and install ``git`` from [link](https://msysgit.github.io/)  
**Jenkins Plugins**  
- Install [Git](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin) Plugin  
- Install [MsBuildPlugin](https://wiki.jenkins-ci.org/display/JENKINS/MSBuild+Plugin) Pugin  
- Install [HtmlPublisherPlugin](https://wiki.jenkins-ci.org/display/JENKINS/HTML+Publisher+Plugin)   
- Install [NUnitPlugin](https://wiki.jenkins-ci.org/display/JENKINS/NUnit+Plugin)  
- Install [Visual Studio Code Metrics](https://wiki.jenkins-ci.org/display/JENKINS/Visual+Studio+Code+Metrics+Plugin) Plugin  
- Install [Cobertura](https://wiki.jenkins-ci.org/display/JENKINS/Cobertura+Plugin) Plugin  

**Tools**  
- [ReportGenerator](https://github.com/danielpalme/ReportGenerator)
- [OpenCover](https://github.com/OpenCover/opencove)
- [Metrics Power Tools](http://www.microsoft.com/en-us/download/confirmation.aspx?id=41647)
- [OpenCoverToCoberturaConverter](http://www.nuget.org/packages/OpenCoverToCoberturaConverter)

###Prepare Environment;  
**Register MsBuild** 
{% highlight bash %}
C:\Program Files (x86)\MSBuild\12.0\Bin\MSBuild.exe
{% endhighlight %}
![Jenkins MSBuild Settings]()  

**Register Visual Studio Code Metrics Tool**   
{% highlight bash %}
C:\Program Files (x86)\Microsoft Visual Studio 12.0\Team Tools\Static Analysis Tools\FxCop\metrics.exe
{% endhighlight %}
![Jenkins Visual Studio Code Metrics Tool Settings]()  

###Create Jenkins Build Job  
**Source Code Management settings**  
![Jenkins Job Source Control Settings]()  

**Build settings**  
_**MSBuild**_  
![MSBUild Settings]()  

_**OpenCover & NUnit**_  
{% highlight bash %}
"C:\Tools\opencover\OpenCover.Console.exe" -target:"C:\Tools\nunit\nunit-console.exe" -targetargs:"%JOB_NAME%.Tests\bin\Debug\%JOB_NAME%.Tests.dll /framework:net-4.5 /xml:%JOB_NAME%NunitTestResults.xml /nologo /noshadow" -filter:"+[*]* -[%JOB_NAME%.Tests]*" -register:Path64 -hideskipped:Filter -output:%JOB_NAME%Coverage.xml
{% endhighlight %}
![OpenCover & NUnit Settings]()  

_**ReportGenerator**_  
{% highlight bash %}
"C:\Tools\reportgenerator\ReportGenerator.exe" -reports:%JOB_NAME%Coverage.xml -targetDir:CodeCoverageHTML
{% endhighlight %}
![ReportGenerator Settings]()  

_**OpenCoverToCoberturaConverter**_  
{% highlight bash %}
"C:\Tools\opencover_to_cobertura_converter\OpenCoverToCoberturaConverter.exe" -input:%JOB_NAME%Coverage.xml -output:%JOB_NAME%Cobertura.xml -sources:%WORKSPACE%
{% endhighlight %}
![OpenCoverToCoberturaConverter Settings]()  

_**Vs Code Metrics Power Tool exec**_  
![Vs Code Metrics Power Tool exec Settings]()  

**Post-Build settings**  

**Pusblish Cobertura Coverage Report**  
![Pusblish Cobertura Coverage Reports]()  

**Pusblish Html Reports**  
![Pusblish Html Reports]()  

**Pusblish NUnit Test Results Report** 
![Pusblish NUnit Test Results Report]()  

**Record VS Code Metrics Power Tool Report**  
![Record VS Code Metrics Power Tool Report]()  
