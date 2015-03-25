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

_**Info:** __I extracted all tools to ``C:\Tools\{toolname}\`` folder._

###Prepare Environment;  
**Register MsBuild** 
{% highlight bash %}
C:\Program Files (x86)\MSBuild\12.0\Bin\MSBuild.exe
{% endhighlight %}
![Jenkins MSBuild Settings]({{ site.url }}/images/post/2015-03-20/2015-03-25_20-39-27.png)  

**Register Visual Studio Code Metrics Tool**   
{% highlight bash %}
C:\Program Files (x86)\Microsoft Visual Studio 12.0\Team Tools\Static Analysis Tools\FxCop\metrics.exe
{% endhighlight %}
![Jenkins Visual Studio Code Metrics Tool Settings]({{ site.url }}/images/post/2015-03-20/2015-03-25_20-37-49.png)  

###Create Jenkins Build Job  
**Source Code Management settings**  
![Jenkins Job Source Control Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-21-27.png)  

**Build settings**  
_**MSBuild**_  
![MSBUild Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-22-11.png)  

_**OpenCover & NUnit**_  
{% highlight bash %}
"C:\Tools\opencover\OpenCover.Console.exe" -target:"C:\Tools\nunit\nunit-console.exe" -targetargs:"%JOB_NAME%.Tests\bin\Debug\%JOB_NAME%.Tests.dll /framework:net-4.5 /xml:%JOB_NAME%NunitTestResults.xml /nologo /noshadow" -filter:"+[*]* -[%JOB_NAME%.Tests]*" -register:Path64 -hideskipped:Filter -output:%JOB_NAME%Coverage.xml
{% endhighlight %}
![OpenCover & NUnit Settings]({{ site.url }}/images/post/2015-03-20/2015-03-25_22-18-14.png)  

_**ReportGenerator**_  
{% highlight bash %}
"C:\Tools\reportgenerator\ReportGenerator.exe" -reports:%JOB_NAME%Coverage.xml -targetDir:CodeCoverageHTML
{% endhighlight %}
![ReportGenerator Settings]({{ site.url }}/images/post/2015-03-20/2015-03-25_22-18-04.png)  

_**OpenCoverToCoberturaConverter**_  
{% highlight bash %}
"C:\Tools\opencover_to_cobertura_converter\OpenCoverToCoberturaConverter.exe" -input:%JOB_NAME%Coverage.xml -output:%JOB_NAME%Cobertura.xml -sources:%WORKSPACE%
{% endhighlight %}
![OpenCoverToCoberturaConverter Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-18-51.png)  

_**Vs Code Metrics Power Tool exec**_  
![Vs Code Metrics Power Tool exec Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-22-30.png)  

**Post-Build settings**  
**Publish Cobertura Coverage Report**  
![Publish Cobertura Coverage Reports]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-11.png)  

**Publish Html Reports**  
![Publish Html Reports]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-43.png)  

**Publish NUnit Test Results Report** 
![Publish NUnit Test Results Report]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-31.png)  

**Record VS Code Metrics Power Tool Report**  
![Record VS Code Metrics Power Tool Report]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-52.png)  
