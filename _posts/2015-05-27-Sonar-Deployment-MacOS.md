---
date: 2015-05-27
layout: post
title: SonarQube Deployment on MacOS
category: tech
tags: SonarQube
---
##Prepare the Database
**Install Tomcat**  
You can refer [this blog](https://wolfpaulus.com/jounal/mac/tomcat8/).  
**Create a database and a user**  
```CREATE DATABASE IF NOT EXISTS sonar;```  
```CREATE USER 'sonar'@'localhost' IDENTIFIED BY 'sonar';```  
```GRANT ALL PRIVILEGES ON sonar.* TO 'sonar'@'localhost' WITH GRANT OPTION;```

##Install the Web Server
**Set the access to the Database**  
Edit ```<install_directory>/conf/sonar.properties``` to configure the database settings.  
Templates are available for every supported database.  
For Tomcat, we need to uncomment the lines below:

```
\# User credentials.  
\# Permissions to create tables, indices and triggers must be granted to JDBC user.  
\# The schema must be created first.  
sonar.jdbc.username=sonar  
sonar.jdbc.password=sonar  
```

```
\#----- MySQL 5.x  
sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance
```

**Start the Web Server**  
Browse the results at http://localhost:9000, if this address doesn't work, check the /logs.
##Install Maven
Edit the settings.xml file, located in ```$MAVEN_HOME/conf or ~/.m2```, to set the database parameters to be used as well as the SonarQube server URL.

```xml
<settings>
    <profiles>
        <profile>
            <id>sonar</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <!-- Example for MySQL-->
                <sonar.jdbc.url>
                  jdbc:mysql://localhost:3306/sonar?useUnicode=true&amp;characterEncoding=utf8
                </sonar.jdbc.url>
                <sonar.jdbc.username>sonar</sonar.jdbc.username>
                <sonar.jdbc.password>sonar</sonar.jdbc.password>
 
                <!-- Optional URL to server. Default value is http://localhost:9000 -->
                <sonar.host.url>
                  http://localhost:9000
                </sonar.host.url>
            </properties>
        </profile>
     </profiles>
</settings>
```
## Code coverage by unit test for Java project
It is possible to feed SonarQube with tests execution and code coverage reports.

Add the pulgin below in your pom.xml build plugins:

```xml
<plugin>
	<groupId>org.jacoco</groupId>
	<artifactId>jacoco-maven-plugin</artifactId>
	<version>${jacoco.version}</version>                   
	<executions>
		<execution>
			<id>default-prepare-agent</id>
			<goals><goal>prepare-agent</goal></goals>
		</execution>
		<execution>
			<id>default-report</id>
			<phase>prepare-package</phase>
			<goals><goal>report</goal></goals>
		</execution>
	</executions>
</plugin>
```
and when you run maven clean install you'll get the report in the "target\site\jacoco" folder which can be read by SonarQube.		
##Analyzing a Maven Project
Analyzing a Maven project consists of running a Maven goal: sonar:sonar in the directory where the pom.xml file sits.  
\#The sonar:sonar goal must be executed in a dedicated mvn command  
```mvn clean install```  
```mvn sonar:sonar```

Then go to http://localhost:9000, and you will get a report about your project like this:
![Y](/images/process/sonar.png)

It seems we have a lot to do with this report :)

##Reference
[Installing Client and Installing the Analyzers](http://docs.sonarqube.org/display/SONAR/Installing#Installing-InstallingClientInstallingtheAnalyzers)
[Installing and Configuring Maven](http://docs.sonarqube.org/display/SONAR/Installing+and+Configuring+Maven)
[Analyzing with Maven](http://docs.sonarqube.org/display/SONAR/Analyzing+with+Maven)

