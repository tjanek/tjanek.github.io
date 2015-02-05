---
layout: post
title: "Hot reloading classes in Spring Boot with Spring Loaded, IntelliJ and Gradle"
date: 2015-02-03 11:40:08 +0100
comments: true
categories: java spring-boot intellij gradle spring-loaded hot-reloading
keywords: spring-boot intellij gradle spring-loaded hot-reloading
---

 Actually almost all modern IDEs (IntelliJ, Eclipse, etc.) gives you support for 
 reloading classes without need to restart the whole application
 using of JDK `HotSwap` mechanism.
 
 Unfortunately, HotSwap has limitations and can reload only changes in method bodies
 and don't support changing method or class signatures ( _at the moment_ ).
 
### Spring Loaded to the rescue

 [Spring Loaded](https://github.com/spring-projects/spring-loaded) is a JVM agent that is actually used as reloading system in `Grails 2.x` and
 has no restrictions to reload changes only in method bodies like HotSwap in JDK.
 It's supports adding, modifying and removing fields, methods, annotations and values in enum out of the box.
 
 
 
### Configuring Spring Loaded with Spring Boot and Gradle in IntelliJ ###

 First of all, we need to setup `Spring Loaded` as build script for Gradle.
 Second, we need enable `idea` plugin and change default output directory for compiled classes for IntelliJ.
 If you don't do that, Gradle and IntelliJ will compile classes into a different locations, so it will cause Spring Loaded to fail.
 
 Also be sure that IntelliJ and Gradle use the same Java versions for compiling classes.
 Setup Java SDK same as Gradle in IntelliJ project structure, otherwise you get a exception when Spring Loaded try to reload a new compiled classes.
 
 Our `build.gradle` should be similar to:
 
 {% codeblock lang:groovy %}
 buildscript {
     repositories {
         mavenCentral()
     }
     dependencies {
         classpath("org.springframework.boot:spring-boot-gradle-plugin:1.2.1.RELEASE")
         // add spring-loaded dependency
         classpath("org.springframework:springloaded:1.2.1.RELEASE")
     }
 }

 
 apply plugin: 'java'
 // apply idea plugin
 apply plugin: 'idea'
 apply plugin: 'spring-boot'
 
 repositories {
     mavenCentral()
 }
 
 dependencies {
     compile("org.springframework.boot:spring-boot-starter-web")
 }
 
 // change default IntelliJ output directory for compiling classes
 idea {
     module {
         inheritOutputDirs = false
         outputDir = file("$buildDir/classes/main/")
     }
 }            
 {% endcodeblock %}
 
 To run `Spring Boot` application from `IntelliJ` IDE, we have to build project or compile single class file after change.
 We can press shortcut for `Build` -> `Make Project` and after build finishes, classes will gets reloaded by Spring Loaded.
 Another option is to record a macro for `Save All` and `Make Project` ( if you have disabled option for auto save all changes in modified files ) and map it to one shortcut, eg. `Ctrl + S`.

 IntelliJ has also a feature called `Make Project Automatically`. If we enable this option, IntelliJ will compiles code automatically after few seconds.
 Unfortunately, this feature only works when application is not running / debugging inside the IDE.
 To make this works together, we must start the application outside the IDE. Example project can be found [here](https://github.com/tjanek/spring-boot-spring-loaded).

 So, if you want reloading system in running Spring Boot application that is not limited to HotSwap mechanism for free, then give Spring Loaded a try.
 Otherwise you may also check [JRebel](http://zeroturnaround.com/software/jrebel/) ( not free ).
 In latest version 6.0, support for Spring Boot was added. JRebel also supports almost all major frameworks and application servers in Java ecosystem. 