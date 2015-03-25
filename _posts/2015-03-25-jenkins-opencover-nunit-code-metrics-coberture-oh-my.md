---
layout: post
title: "Useful Jenkins Plugins and Configuration"
description: Jenkins, OpenCover, NUnit, Code Metrics, Cobertura Oh my!
headline: 
category: engineering
tags: [jenkins, opencover, code, metrics, cobertura, ci, git, continuous integration]
comments: true
mathjax: 
---

## Jenkins, OpenCover, NUnit, Code Metrics, Cobertura Oh my!

**TODO:** Abstract

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
{% highlight bash %}  
https://github.com/{username}/{projectname}.git
{% endhighlight %}  
![Jenkins Job Source Control Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-21-27.png)  

**Build settings**  
_**MSBuild**_  
Configure ``Jenkins`` job to build source code using ``MSBuild``.  
{% highlight bash %}  
{projectname}.sln
{% endhighlight %}  
![MSBUild Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-22-11.png)  

_**OpenCover & NUnit**_  
Add build step to generate ``OpenCover`` coverage reults using ``NUit`` test runner.  
{% highlight bash %}  
"C:\Tools\opencover\OpenCover.Console.exe" -target:"C:\Tools\nunit\nunit-console.exe" -targetargs:"%JOB_NAME%.Tests\bin\Debug\%JOB_NAME%.Tests.dll /framework:net-4.5 /xml:%JOB_NAME%NunitTestResults.xml /nologo /noshadow" -filter:"+[*]* -[%JOB_NAME%.Tests]*" -register:Path64 -hideskipped:Filter -output:%JOB_NAME%Coverage.xml
{% endhighlight %}  
![OpenCover & NUnit Settings]({{ site.url }}/images/post/2015-03-20/2015-03-25_22-18-14.png)  

_**ReportGenerator**_  
Add build step to generate html results from ``OpenCover`` coverage reults.  
{% highlight bash %}
"C:\Tools\reportgenerator\ReportGenerator.exe" -reports:%JOB_NAME%Coverage.xml -targetDir:CodeCoverageHTML
{% endhighlight %}  
![ReportGenerator Settings]({{ site.url }}/images/post/2015-03-20/2015-03-25_22-18-04.png)  

_**OpenCoverToCoberturaConverter**_  
Add build step to convert ``opencover`` results to ``cobertura`` reports.  
{% highlight bash %}
"C:\Tools\opencover_to_cobertura_converter\OpenCoverToCoberturaConverter.exe" -input:%JOB_NAME%Coverage.xml -output:%JOB_NAME%Cobertura.xml -sources:%WORKSPACE%
{% endhighlight %}
![OpenCoverToCoberturaConverter Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-18-51.png) 

_**Vs Code Metrics Power Tool exec**_  
Add build step to calculate ``Code Metrics``.  
{% highlight bash %}
"C:\Tools\opencover_to_cobertura_converter\OpenCoverToCoberturaConverter.exe" -input:%JOB_NAME%Coverage.xml -output:%JOB_NAME%Cobertura.xml -sources:%WORKSPACE%
{% endhighlight %}
![Vs Code Metrics Power Tool exec Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-22-30.png)  

**Post-Build settings**  
_**Publish Cobertura Coverage Report**_  
{% highlight bash %}
{YourProjectName}Cobertura.xml
{% endhighlight %}
Add post-build step to view code coverage results as ``Cobertura`` report. 
![Publish Cobertura Coverage Reports]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-11.png)  

_**Publish Html Reports**_  
{% highlight bash %}
CodeCoverageHTML   # directory
index.htm          # report index page
Code Coverage      # report title
{% endhighlight %}
Add post-build step to view code coverage results as html. 
![Publish Html Reports]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-43.png)  

_**Publish NUnit Test Results Report**_  
Add post-build step to view ``NUnit`` Test results.  
{% highlight bash %}
{YourProjectName}NunitTestResults.xml
{% endhighlight %}
![Publish NUnit Test Results Report]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-31.png)  

_**Record VS Code Metrics Power Tool Report**_  
Add post-build step to view  ``VS Code Metrics Power Tool`` Reports.  
{% highlight bash %}
{YourProjectName}Metrics.xml
{% endhighlight %}
![Record VS Code Metrics Power Tool Report]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-52.png)
