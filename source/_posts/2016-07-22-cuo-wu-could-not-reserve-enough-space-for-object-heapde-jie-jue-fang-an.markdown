---
layout: post
title: "错误:Could not reserve enough space for object heap的解决方案"
date: 2016-07-22 20:59:57 +0800
comments: true
categories: 问题集
---

#错误:Could not reserve enough space for object heap的解决方案

##Reason:
If you go thru this IBM link on java, it says that on 32 bit windows the recommended heap size is 1.5 GB and the Maximum heap size is 1.8 GB. So your jvm does not gets initialized for -Xmx2G and above.

Also if you go thru this SO answer, clearly the DLL bindings are an issue for memory reservation changing which is no trivial task. Hence what may be recommended is that you go for 64-bit Windows and a 64-bit JVM. while it will chew up more RAM, you will have much more contiguous virtual address space.

##Method to fix:
> For Windows

Go to Start → Control Panel → System → Advanced system settings → advanced(tab) → Environment Variables → System Variables → New:

Variable name: _JAVA_OPTIONS
Variable value: -Xmx512M

> For Linux

```
export _JAVA_OPTIONS=-Xmx512M
``` 
or 
```
export _JAVA_OPTIONS=-Xmx1024M 
```

> For Android Studio

Project > Gradle Scripts > gradle.properties

org.gradle.jvmargs=-Xms512m -Xmx1024m

