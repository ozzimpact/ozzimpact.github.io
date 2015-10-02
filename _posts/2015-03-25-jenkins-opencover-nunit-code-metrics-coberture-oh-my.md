---
layout: post
title: "Useful Jenkins Plugins and Configuration"
description: Jenkins, OpenCover, NUnit, Code Metrics, Cobertura Oh my!
headline: 
category: development
tags: [jenkins, opencover, code, metrics, cobertura, ci, git, continuous integration]
comments: true
mathjax: 
---

## Jenkins, OpenCover, NUnit, Code Metrics, Cobertura Oh my!  
In this tutorial, we will be talking about how to provide ```CI(Continuous Integration)``` for ```.NET``` projects using ```Jenkins```. And how to be informed about code and test coverage and code metrics.   
_**Pre-Build**_  
 • Ensure that Jenkins and Git installed properly with necessary plugins and tools which are defined below.(_**Info**: Extract all tools to ``C:\Tools\{toolname}\`` folder._)  
 • Jenkins settings should be configured to be able to run C# project and code metrics tool which is needed to prepare report, properly. Meaning by that, MSBuild and Visual Studio Code Metrics Tool should be registered.   
_**Build**_   
 • Source code should be pulled to Jenkins from git or any other scm repository.(In this example Github is used.) A Jenkins job is created for this action.  
 • After pulling the project, another Jenkins job is configured to build solution using MSBuildPlugin which is installed earlier.  
 • Afterwards, build step should be set to generate OpenCover coverage result using NUnit test runner and another build step should be added to get OpenCover coverage result as an HTML file.  
 • If you want to convert OpenCover coverage results to Cobertura reports, another build step should be added. There is a nuget package to make this easier which is OpenCoverToCoberturaConverter. ([Why Cobertura?](https://github.com/cobertura/cobertura/wiki/FAQ))    
 • After reports, another build step is added to calculate code metrics using VS Code Metrics Power Tool.  
 _**Post-Build**_  
 • As a post-build action, publishing Cobertura coverage report step should be added using Cobertura for Jenkins.   
 • And also Jenkins provides publishing reports as HTML files. Again, another post-build step should be added for this using HtmlPublisherPlugin.   
 • To publish NUnit test results as a post-build action, NUnitPlugin is configured.  
 • To publish code metrics as a post-build action, Record VS Code Metrics Power Tool Report is configured.  

Requirements  
---------------------------------------------------------
**Jenkins**  
Please use instructions from here [Installing Jenkins on Windows](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins)  
**Git**  
Download and install ``git`` from [link](https://msysgit.github.io/)  

**Jenkins Plugins**  
• [Git](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin)  
• [MsBuildPlugin](https://wiki.jenkins-ci.org/display/JENKINS/MSBuild+Plugin)  
• [HtmlPublisherPlugin](https://wiki.jenkins-ci.org/display/JENKINS/HTML+Publisher+Plugin)   
• [NUnitPlugin](https://wiki.jenkins-ci.org/display/JENKINS/NUnit+Plugin)    
• [Visual Studio Code Metrics](https://wiki.jenkins-ci.org/display/JENKINS/Visual+Studio+Code+Metrics+Plugin)    
• [Cobertura](https://wiki.jenkins-ci.org/display/JENKINS/Cobertura+Plugin)  

**Tools**  
• [ReportGenerator](https://github.com/danielpalme/ReportGenerator/releases)  
• [OpenCover](https://github.com/OpenCover/opencover/releases)  
• [Metrics Power Tools](http://www.microsoft.com/en-us/download/confirmation.aspx?id=41647)  
• [OpenCoverToCoberturaConverter](http://www.nuget.org/packages/OpenCoverToCoberturaConverter)  
• [NUnit](http://nunit.org/?p=download)  
_**Info:** Extract all tools(Metrics Power Tools will be installed in Program Files) to ``C:\Tools\{toolname}\`` folder._

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
**Source Code Management Settings**  
Configure ``Jenkins`` job to get source code from ``Git`` repository.  
{% highlight bash %}  
https://github.com/{projectname}.git
{% endhighlight %}  
![Jenkins Job Source Control Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-21-27.png)  

**Build settings**  
_**MSBuild**_  
Configure ``Jenkins`` job to build source code using ``MSBuild``.  
{% highlight bash %}  
{projectname}.sln
{% endhighlight %}  
![MSBUild Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-18-31.png)  

_**OpenCover & NUnit**_  
Add build step to generate ``OpenCover`` coverage results using ``NUnit`` test runner.  
{% highlight bash %}  
"C:\Tools\opencover\OpenCover.Console.exe" -target:"C:\Tools\nunit\nunit-console.exe" -targetargs:"%JOB_NAME%.Tests\bin\Debug\%JOB_NAME%.Tests.dll /framework:net-4.5 /xml:%JOB_NAME%NunitTestResults.xml /nologo /noshadow" -filter:"+[*]* -[%JOB_NAME%.Tests]*" -register:Path64 -hideskipped:Filter -output:%JOB_NAME%Coverage.xml
{% endhighlight %}  
![OpenCover & NUnit Settings]({{ site.url }}/images/post/2015-03-20/2015-03-25_22-18-14.png)  

_**ReportGenerator**_  
Add build step to generate html results from ``OpenCover`` coverage results.  
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

_**VS Code Metrics Power Tool**_  
Add build step to calculate ``Code Metrics``.  
{% highlight bash %}
Your assemblies to calculate code metrics.
{% endhighlight %}
![Vs Code Metrics Power Tool exec Settings]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-19-54.png)  

**Post-Build settings**  
_**Publish Cobertura Coverage Report**_  
{% highlight bash %}
{YourProjectName}Cobertura.xml
{% endhighlight %}
Add post-build step to view code coverage results as ``Cobertura`` report. 
![Publish Cobertura Coverage Reports]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-11.png)  

_**Publish Html Reports**_  
{% highlight bash %}
CodeCoverageHTML   # HTML directory to archive
index.htm          # report index page
Code Coverage      # report title
{% endhighlight %}
Add post-build step to view code coverage results as html. 
![Publish Html Reports]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-43.png)  

_**Publish NUnit Test Results Report**_  
Add post-build step to view ``NUnit`` test results.  
{% highlight bash %}
{YourProjectName}NunitTestResults.xml
{% endhighlight %}
![Publish NUnit Test Results Report]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-31.png)  

_**Record VS Code Metrics Power Tool Report**_  
Add post-build step to view  ``VS Code Metrics Power Tool`` reports.  
{% highlight bash %}
{YourProjectName}Metrics.xml
{% endhighlight %}
![Record VS Code Metrics Power Tool Report]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-25_22-20-52.png)

Result
---------------------------------------------------------
**Tests**  
![Test]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-26_00-24-41.png)  
**Code Coverage - OpenCover**  
![Code Coverage - OpenCover]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-26_00-25-28.png)  

**Code Coverage - Cobertura**  
![Code Coverage - Cobertura]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-26_00-26-18.png)  
![Code Coverage - Cobertura]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-26_00-25-48.png)  

**Metrics**  
![Metrics]({{ site.url }}/images%2Fpost%2F2015-03-20%2F2015-03-26_00-24-05.png)  


_**This is a collaborative post with [Ziya Sarıkaya](http://ziyasal.github.io/development/redis-setup%20and-high-availability/).**_
