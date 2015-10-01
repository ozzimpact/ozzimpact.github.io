---
layout: post
title: "Log4j Configuration"
description: Log4j, java, logging, log level
headline:
category: development
tags: [Log4j, java, code, logging, log level]
comments: true
mathjax:
---



Here is the example of the most popular log mechanism of Java, Log4j. In this one, I create separate log files for each log level. With this configuration every log file contains only its level information. As an example, info.log shows only info logs not warn or error logs. And also you can choose log severity from the top of the file `log4j.rootlogger=TRACE`. While making this example, I struggled about creating separate files and not showing different level's log in one file. At the end I asked this problem in stackoverflow. Then I found similar problems and their solution. [Here](http://stackoverflow.com/questions/28231893/log4j-how-to-log-by-level-and-the-log-file-should-contains-only-its-levels-log) you can see.


```
log4j.rootLogger=TRACE,TraceFileAppender,DebugFileAppender,InfoFileAppender,WarnFileAppender,ErrorFileAppender,FatalFileAppender

# TraceFileAppender - used to log messages in the trace.log file.
log4j.appender.TraceFileAppender=org.apache.log4j.FileAppender
log4j.appender.TraceFileAppender.File=./logs/trace.log
log4j.appender.TraceFileAppender.layout=org.apache.log4j.PatternLayout
log4j.appender.TraceFileAppender.filter.A=org.apache.log4j.varia.LevelRangeFilter
log4j.appender.TraceFileAppender.filter.A.LevelMin=TRACE
log4j.appender.TraceFileAppender.filter.A.LevelMax=TRACE
log4j.appender.TraceFileAppender.layout.ConversionPattern=[%-5p]%d{yyyy-MM-dd HH:mm:ss} - %m%n

# DebugFileAppender - used to log messages in the debug.log file.
log4j.appender.DebugFileAppender=org.apache.log4j.FileAppender
log4j.appender.DebugFileAppender.File=./logs/debug.log
log4j.appender.DebugFileAppender.layout=org.apache.log4j.PatternLayout
log4j.appender.DebugFileAppender.filter.B=org.apache.log4j.varia.LevelRangeFilter
log4j.appender.DebugFileAppender.filter.B.LevelMin=DEBUG
log4j.appender.DebugFileAppender.filter.B.LevelMax=DEBUG
log4j.appender.DebugFileAppender.layout.ConversionPattern=[%-5p]%d{yyyy-MM-dd HH:mm:ss} - %m%n

# InfoFileAppender - used to log messages in the info.log file.
log4j.appender.InfoFileAppender=org.apache.log4j.FileAppender
log4j.appender.InfoFileAppender.File=./logs/info.log
log4j.appender.InfoFileAppender.layout=org.apache.log4j.PatternLayout
log4j.appender.InfoFileAppender.filter.C=org.apache.log4j.varia.LevelRangeFilter
log4j.appender.InfoFileAppender.filter.C.LevelMin=INFO
log4j.appender.InfoFileAppender.filter.C.LevelMax=INFO
log4j.appender.InfoFileAppender.layout.ConversionPattern=[%-5p]%d{yyyy-MM-dd HH:mm:ss} - %m%n

# WarnFileAppender - used to log messages in the warn.log file.
log4j.appender.WarnFileAppender=org.apache.log4j.FileAppender
log4j.appender.WarnFileAppender.File=./logs/warn.log
log4j.appender.WarnFileAppender.layout=org.apache.log4j.PatternLayout
log4j.appender.WarnFileAppender.filter.D=org.apache.log4j.varia.LevelRangeFilter
log4j.appender.WarnFileAppender.filter.D.LevelMin=WARN
log4j.appender.WarnFileAppender.filter.D.LevelMax=WARN
log4j.appender.WarnFileAppender.layout.ConversionPattern=[%-5p]%d{yyyy-MM-dd HH:mm:ss} - %m%n

# ErrorFileAppender - used to log messages in the error.log file.
log4j.appender.ErrorFileAppender=org.apache.log4j.FileAppender
log4j.appender.ErrorFileAppender.File=./logs/error.log
log4j.appender.ErrorFileAppender.layout=org.apache.log4j.PatternLayout
log4j.appender.ErrorFileAppender.filter.E=org.apache.log4j.varia.LevelRangeFilter
log4j.appender.ErrorFileAppender.filter.E.LevelMin=ERROR
log4j.appender.ErrorFileAppender.filter.E.LevelMax=ERROR
log4j.appender.ErrorFileAppender.layout.ConversionPattern=[%-5p]%d{yyyy-MM-dd HH:mm:ss} - %m%n

# FatalFileAppender - used to log messages in the fatal.log file.
log4j.appender.FatalFileAppender=org.apache.log4j.FileAppender
log4j.appender.FatalFileAppender.File=./logs/fatal.log
log4j.appender.FatalFileAppender.layout=org.apache.log4j.PatternLayout
log4j.appender.FatalFileAppender.filter.F=org.apache.log4j.varia.LevelRangeFilter
log4j.appender.FatalFileAppender.filter.F.LevelMin=FATAL
log4j.appender.FatalFileAppender.filter.F.LevelMax=FATAL
log4j.appender.FatalFileAppender.layout.ConversionPattern=[%-5p]%d{yyyy-MM-dd HH:mm:ss} - %m%n
```
Here is java code snippet to initialize log4j.properties:

```java
 PropertyConfigurator.configure("log4j.properties");
 private org.apache.log4j.Logger _logger = org.apache.log4j.Logger.getRootLogger();
```
