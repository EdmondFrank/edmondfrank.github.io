---
layout: post
title: "Java Dev tools and Debugging notes"
date: 2020-10-26 23:27:35 +0800
comments: true
categories: Java
---
# Jave Dev tools and Debugging Notes

1.  [Java](#org1b5c54c)
    1.  [Maven](#org65d3833)
        1.  [use maven with proxy and skip tests:](#org6c8dc4e)
        2.  [Structure](#orge347d7a)
    2.  [JShell](#orgb3fa18c)
    3.  [Javap](#orgd240f02)
    4.  [Jar Files](#org6db32e3)
        1.  [compile and package as a FAT-JAR](#orgef3a76f)
        2.  [differences between "java -cp" and "java -jar"](#orgc84a88c)
    5.  [Debugging](#orge743cbc)
        1.  [Exec args:](#org8d022e6)
        2.  [Jdb](#orgdc3e49d)


<a id="org1b5c54c"></a>

# Java

Some notes about Java dev tools and debuggers


<a id="org65d3833"></a>

## Maven

**build lifecycle targets**

-   **validate**: validate the project is correct and all necessary information is available
-   **compile:** compile the source code of the project
-   **test**: test the compiled source code using a suitable unit testing framework. There tests should not require the code be packaged or deployed
-   **package**: take the compiled code and package it in its distributable format, such as JAR
-   **verify**: run any checks on results of integration tests to ensure quality criteria are met
-   **install**: install the package into the local repository, for use as a dependency in other projects locally
-   **deploy**: done in the build environment, copies the final package to the remote repository for sharing with other developers and projects


<a id="org6c8dc4e"></a>

### use maven with proxy and skip tests:

-Dmaven.test.skip=true
-Dhttps.proxyHost=127.0.0.1
-Dhttps.proxyPort=1081
-Dhttp.proxyHost=127.0.0.1
-Dhttp.proxyPort=1081


<a id="orge347d7a"></a>

### Structure

1.  Expected directory structure:
    -   Java files are in src/main/java as well as src/test/java.
    -   Resource files are under src/main/resources and src/test/resources.
2.  **mvn archetype:generate**: Generates a skeleton of a project based on your inputs (package name, versioning, project name, etc)
3.  Edit pom.xml and set the jdk version there..
4.  mvn package - compile, test, bundle.


<a id="orgb3fa18c"></a>

## JShell

Java REPL(Read Eval Print Loop) import after Java 9

-   **/list** -start - shows modules imported at startup.
-   **/edit <number>** - edit that line in a new window.
-   **/set editor "vi"** - use vi instead of the default graphical edit pad.
-   **/save abc.java** - save current buffer to file.
-   **/load abc.java** - load from file into shell.
-   **/-1** - execute last snippet.
-   **/1** - execute first snippet.
-   **/drop N** - drop Nth snippet.
-   **/vars** - show only variables that were defined in snippets.
-   **/types** - show only classes that were defined in snippets.


<a id="orgd240f02"></a>

## Javap

javap TestDecompile.class - decompile .class file to human readable format. Does not show content of methods though.
javap -c TestDecompile.class - show jvm bytecode in human readable -form, including methods.


<a id="org6db32e3"></a>

## Jar Files

These are zip files that have a META-INF folder with a Manifest.mf file inside.


<a id="orgef3a76f"></a>

### compile and package as a FAT-JAR

    <?xml version="1.0" encoding="UTF-8"?>
    <build>
       <finalName>indexer-spider</finalName>
       <plugins>
          <plugin>
             <groupId>org.apache.maven.plugins</groupId>
             <artifactId>maven-assembly-plugin</artifactId>
             <version>2.4.1</version>
             <configuration>
                <!--  get all project dependencies  -->
                <descriptorRefs>
                   <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs>
                <!--  MainClass in mainfest make a executable jar  -->
                <archive>
                   <manifest>
                      <mainClass>org.apache.maven.indexer.examples.BasicUsageExample</mainClass>
                   </manifest>
                </archive>
             </configuration>
             <executions>
                <execution>
                   <id>make-assembly</id>
                   <!--  bind to the packaging phase  -->
                   <phase>package</phase>
                   <goals>
                      <goal>single</goal>
                   </goals>
                </execution>
             </executions>
          </plugin>
          <plugin>
             <groupId>org.apache.maven.plugins</groupId>
             <artifactId>maven-compiler-plugin</artifactId>
             <configuration>
                <source>8</source>
                <target>8</target>
             </configuration>
          </plugin>
       </plugins>
    </build>


<a id="orgc84a88c"></a>

### differences between "java -cp" and "java -jar"

-   There won't be any difference in terms of performance.
-   **java -cp:** must specify the required classes and jar's in the classpath for running a java class file.
-   **java -jar:** jvm finds the class that it needs to run from **/META-INF/MANIFEST.MF** file inside the jar file


<a id="orge743cbc"></a>

## Debugging

-   **jps** - Shows all runnning java processes.
-   **jvisualvm** - If you have it, it shows the java processes on the system with details on threads, profiler etc.
-   **debugging mode** - Use these args to start a process in debugging mode


<a id="org8d022e6"></a>

### Exec args:

`-Xagentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=<port>`

-   **agentlib=jdwp** - Load the jdwp agent, for debugging.
-   **transport=dt_socket** - for connecting a debugging client over the network.
-   **server=y** - this one is the server half of the debugging.
-   **suspend=y** - don't start executing until a client debugger attaches.
-   **address=7777** - port we listen on.


<a id="orgdc3e49d"></a>

### Jdb

-   **jdb -attach <port>**: attach debug process start with debugging mode.

