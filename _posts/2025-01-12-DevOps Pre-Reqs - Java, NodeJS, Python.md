---
title: DevOps Pre-Reqs - Java, NodeJS, Python
date: 2025-01-12 12:00:00 -500
categories: [DevOps,Coding]
tags: [Java,Code,NodeJS,Python]

---

> [!quote] Our distrust is very expensive.
> — Ralph Emerson

---

## DevOps Pre-Reqs - Java, NodeJS, Python

## Complied Code

1- Develop Source Code
2- Compile
```java
javac code.java
```
3- Run
```java
java code
```

Interpreted Code

1- Dev Source Code
2- Run

Source Code > Compiler > Interpreter
Human Readable Source Code > Intermediary Byte Code > Machine Readable Machine Code

## Packages

Filesystems, Math, OS, HTTP, Security, Networking, ETC... Also known dependencies.
## Build Process

- Compile
- Run Tests
- Package
- Delivery

---


## Java

```bash
wget https://download.java.net<what file>
```

UN-Compress
```bash
tar -xvf <File>
```

Check the file version 
```bash
jdk-13.0.2/bin/java -version
```

```bash
java -version
```

# JDK - Java Dev Kit 

Develop - Any IDE written then use `jdb`, `javadoc` for debugger
Build - `javac`, `jar` will help you compile
Run - `JRE`(Java Runtime Environment) need this to run and the loader `java`

After V9 you can install all JRE standalone but now its part of JDK

Lab
```bash
https://download.java.net/java/GA/jdk20/bdc68b4b9cbc4ebcb30745c85038d91d/36/GPL/openjdk-20_linux-x64_bin.tar.gz --output /opt/openjdk-20_linux-x64_bin.tar.gz
```

```bash
sudo tar -xf openjdk-20_linux-x64_bin.tar.gz -C /opt/
```

```bash
jdk-20/bin/java -version
```

Set java to the PATH
```bash
export PATH=$PATH:/opt/jdk-20/bin
```


## Package

JAR = Java Archive 

A JAR (Java Archive) file is a package format used by the Java platform to bundle multiple files into a single compressed archive, typically including:

- Compiled Java classes (.class files)
- Configuration data (such as `.properties` files)
- Resource files (images, text files, etc.)
- Metadata (manifest file in `META-INF/` directory)

It leverages standard ZIP compression with a `.jar` extension, facilitating easier distribution and deployment of Java applications and libraries. When a JAR file is marked as “executable” (containing a `Main-Class` attribute in its manifest), it can be run directly using the `java -jar` command.

WAR = Web Archive


```bash
jar cf TheApp.jar class1.c1ass Servicel.class Service2.c1ass
```

Manifest info about the file can be found in META-INF/MANIFEST.MF

of the many files what is the front door file that can be run it can be found in the manifest


```bash
java -jar TheApp.jar
```

## Java Documenting

```bash
javadoc -d doc TheClass.java
```

## Build Tools

- Maven
- Gradle
- ANT

Compile > package > Document

### ANT

build.xml

![[Pasted image 20250112180408.png]]

### Maven

All java files come with build files with maven and the docs tell you how to run. 

[https://github.com/arun-gupta/docker-java-sample](https://github.com/arun-gupta/docker-java-sample)

### Gradle

build.gradle and then the commands

See above link for arun gupta java sample site.


```bash
/**
 * Prints Hello World Message
 *
 */

public class MyClass {
    
    /**
     * Default constructor for the MyClass class.
     */

    public MyClass() {
    }

    /**
     * The main method that prints the message.
     *
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        System.out.println("Hello Mr CodeMan");
    }
}
```


```bash
sudo yum install -y ant
```

build.xml
```bash
<?xml version="1.0"?>
<project name="Ant" default="main" basedir=".">
    <!-- Compiles the java code (including the usage of library for JUnit -->
    <target name="compile">
        <javac srcdir="/opt/ant/src" destdir="/opt/ant/build">
        </javac>
    </target>
    <!-- Creates Javadoc -->
    <target name="docs" depends="compile">
        <javadoc packagenames="src" sourcepath="/opt/ant/src" destdir="/opt/ant/docs">
            <!-- Define which files / directory should get included, we include all -->
            <fileset dir="/opt/ant/src">
                <include name="**" />
            </fileset>
        </javadoc>
    </target>
    <!--Creates the deployable jar file  -->
    <target name="jar" depends="compile">
        <jar basedir="/opt/ant/build" destfile="/opt/ant/dist/MyClass.jar" >
            <manifest>
                <attribute name="Main-Class" value="MyClass" />
            </manifest>
        </jar>
    </target>
    <target name="run" depends="jar">
      <java jar="/opt/ant/dist/MyClass.jar" fork="true" />
    </target>
    <target name="main" depends="compile, jar, docs, run">
        <description>Main target</description>
    </target>
</project>
```


Compile using ANT
```bash
ant compile jar
```



```bash
sudo yum install -y maven
```

```bash
cat /opt/maven/my-app/pom.xml | grep artifactId
```

```bash
cd /opt/maven/my-app/; sudo mvn package
```


run maven app
```bash
java -cp /opt/maven/my-app/target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App
```

The command java -cp /opt/maven/my-app/target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App is used to execute a Java application from the command line. Here's a breakdown of its components:

java: This invokes the Java Runtime Environment (JRE) to run a Java application.

-cp /opt/maven/my-app/target/my-app-1.0-SNAPSHOT.jar: The -cp (or -classpath) option specifies the classpath, which tells the JRE where to find the compiled classes and resources. In this case, it points to the JAR file generated by Maven during the build process.

com.mycompany.app.App: This is the fully qualified name of the main class to be executed. It includes the package name (com.mycompany.app) and the class name (App).

This command is typically run after compiling and packaging a Maven project to execute the application directly from the command line.

For more detailed information on running Java applications from the command line, you can refer to the Apache Maven tutorial. 
[https://cwiki.apache.org/confluence/display/MAVEN/Tutorial%3A%2BBuild%2Ba%2BJAR%2Bfile%2Bwith%2BMaven%2Bin%2B5%2Bminutes?utm_source=chatgpt.com](https://cwiki.apache.org/confluence/display/MAVEN/Tutorial%3A%2BBuild%2Ba%2BJAR%2Bfile%2Bwith%2BMaven%2Bin%2B5%2Bminutes?utm_source=chatgpt.com)

```bash

```



```bash

```



```bash

```



```bash

```



```bash

```



```bash

```



```bash

```



```bash

```








