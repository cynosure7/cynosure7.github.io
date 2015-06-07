---
date: 2014-03-06
layout: post
title: How to Build Hadoop 2.2.0 in CentOS (64bit)
category: tech
tags: Hadoop CentOS 64bit
---

##preparation:

Vmvare CentOS release 6.3 (Final) with Internet access

##1. install jdk:

[eva@hadoop1 ~]$sudo su

[root@hadoop1 eva]#cd /home/eva  
[root@hadoop1 eva]#mkdir java  
[root@hadoop1 eva]#cd java

[root@hadoop1 eva]#wget --no-cookie --no-check-certificate --header "Cookie: gpw\_e24=http%3A%2F%2Fwww.oracle.com%2F" http://download.oracle.com/otn-pub/java/jdk/7u51-b13/jdk-7u51-linux-x64.rpm  
[root@hadoop1 eva]#mv jdk-7u51-linux-x64.rpm\?AuthParam\=1390704930_5bceb3152f3738eedc3414ead982e092 jdk-7u51-linux-x64.rpm

[root@hadoop1 eva]#rpm -ivh jdk-7u51-linux-x64.rpm

[root@hadoop1 eva]#vi /etc/profile  
_export JAVA\_HOME=/usr/java/jdk1.7.0\_51  
export CLASSPATH=.:$JAVA\_HOME/jre/lib/rt.jar:$JAVA\_HOME/lib/dt.jar:$JAVA\_HOME/lib/tools.jar  
export PATH=$PATH:$JAVA\_HOME/bin_

[root@hadoop1 eva]#source /etc/profile  
[eva@hadoop1 ~]$java -version  
_java version "1.7.0\_51"  
Java(TM) SE Runtime Environment (build 1.7.0\_51-b13)  
Java HotSpot(TM) 64-Bit Server VM (build 24.51-b03, mixed mode)_  

##2. install maven
[eva@hadoop1 ~]$cd /usr  
[eva@hadoop1 ~]$wget http://apache.claz.org/maven/maven-3/3.0.5/binaries/apache-maven-3.0.5-bin.tar.gz  
[eva@hadoop1 ~]$tar zxvf apache-maven-3.0.5-bin.tar.gz

[eva@hadoop1 ~]$vi /etc/profile  
_export M2\_HOME=/usr/apache-maven-3.0.5  
export M2=$M2\_HOME/bin  
export PATH=$M2:$PATH  
source /etc/profile_   

[eva@hadoop1 ~]$mvn -v  
_Apache Maven 3.0.5 (r01de14724cdef164cd33c7c8c2fe155faf9602da; 2013-02-19 13:51:28+0000)  
Maven home: /usr/local/apache-maven-3.0.5  
Java version: 1.7.0\_51, vendor: Oracle Corporation  
Java home: /usr/java/jdk1.7.0\_51/jre  
Default locale: en\_US, platform encoding: UTF-8  
OS name: "linux", version: "2.6.32-279.9.1.el6.x86\_64", arch: "amd64", family: "unix"_

##3. install basic kit
[eva@hadoop1 ~]$yum -y install gcc  gcc-c++  svn  cmake git zlib zlib-devel openssl openssl-devel rsync  

_Error: Package: gcc-c++-4.4.6-4.el6.x86\_64 (qa\_os\_centos6.3\_64)_  
           Requires: libstdc++-devel = 4.4.6-4.el6  
_You could try using --skip-broken to work around the problem_  
_You could try running: rpm -Va --nofiles --nodigest_
  
search "libstdc++-devel = 4.4.6-4.el6" in Google, and find the download link, copy the link:
ftp://ftp.icm.edu.pl/vol/rzm2/linux-scientificlinux/6.3/x86_64/os/Packages/libstdc++-devel-4.4.6-4.el6.x86_64.rpm

[eva@hadoop1 ~]$cd /usr  
[eva@hadoop1 ~]$wget ftp://ftp.icm.edu.pl/vol/rzm2/linux-scientificlinux/6.3/x86\_64/os/Packages/libstdc++-devel-4.4.6-4.el6.x86\_64.rpm  
[eva@hadoop1 ~]$rpm -ivh libstdc++-devel-4.4.6-4.el6.x86\_64.rpm  

##4.install protobuf
[eva@hadoop1 ~]$sudo su  
[eva@hadoop1 ~]$cd /usr  
[eva@hadoop1 ~]$wget https://protobuf.googlecode.com/files/protobuf-2.5.0.tar.bz2  
[eva@hadoop1 ~]$tar xvf protobuf-2.5.0.tar.bz2  
[eva@hadoop1 ~]$cd protobuf-2.5.0  
[eva@hadoop1 ~]$./configure  

##5.get Hadoop 2.0 source code and build
[eva@hadoop1 ~]$cd /usr  
[eva@hadoop1 ~]$mkdir hadoop  
[eva@hadoop1 ~]$cd hadoop  
[eva@hadoop1 ~]$wget http://ftp.mirror.tw/pub/apache/hadoop/common/hadoop-2.2.0/hadoop-2.2.0-src.tar.gz  
[eva@hadoop1 ~]$tar zxvf hadoop-2.2.0-src.tar.gz  
[eva@hadoop1 ~]$cd hadoop-2.2.0-src  
[eva@hadoop1 ~]$mvn package -Pdist,native -DskipTests -Dtar  

we got errors here:  
_[INFO] 4 errors  
[INFO] -------------------------------------------------------------  
[INFO] ------------------------------------------------------------------------  
[INFO] Reactor Summary:  
[INFO]  
[INFO] Apache Hadoop Main ................................ SUCCESS [17:57.790s]  
[INFO] Apache Hadoop Project POM ......................... SUCCESS [11:34.011s]  
[INFO] Apache Hadoop Annotations ......................... SUCCESS [10:47.776s]  
[INFO] Apache Hadoop Assemblies .......................... SUCCESS [0.246s]  
[INFO] Apache Hadoop Project Dist POM .................... SUCCESS [5:54.528s]  
[INFO] Apache Hadoop Maven Plugins ....................... SUCCESS [8:50.253s]  
[INFO] Apache Hadoop Auth ................................ FAILURE [2:29.481s]  
...  
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:2.5.1:testCompile (default-testCompile) on project hadoop-auth: Compilation failure: Compilation failure:  
[ERROR] /usr/hadoop/hadoop-2.2.0-src/hadoop-common-project/hadoop-auth/src/test/java/org/apache/hadoop/security/authentication/client/AuthenticatorTestCase.java:[88,11] error: cannot access AbstractLifeCycle  
[ERROR] class file for org.mortbay.component.AbstractLifeCycle not found  
[ERROR] /usr/hadoop/hadoop-2.2.0-src/hadoop-common-project/hadoop-auth/src/test/java/org/apache/hadoop/security/authentication/client/AuthenticatorTestCase.java:[96,29] error: cannot access LifeCycle  
[ERROR] class file for org.mortbay.component.LifeCycle not found  
[ERROR] /usr/hadoop/hadoop-2.2.0-src/hadoop-common-project/hadoop-auth/src/test/java/org/apache/hadoop/security/authentication/client/AuthenticatorTestCase.java:[98,10] error: cannot find symbol  
[ERROR] symbol:   method start()  
[ERROR] location: variable server of type Server  
[ERROR] /usr/hadoop/hadoop-2.2.0-src/hadoop-common-project/hadoop-auth/src/test/java/org/apache/hadoop/security/authentication/client/AuthenticatorTestCase.java:[104,12] error: cannot find symbol  
[ERROR] -> [Help 1]_  

search "Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:2.5.1:testCompile" in Google, and find the root cause:
https://issues.apache.org/jira/browse/HADOOP-10110

as https://issues.apache.org/jira/secure/attachment/12614482/HADOOP-10110.patch says,
we need to add   

      <artifactId>jetty-util</artifactId>  
      <scope>test</scope>  
    </dependency>  
    <dependency>  
      <groupId>org.mortbay.jetty</groupId>  

in hadoop-common-project/hadoop-auth/pom.xml  

after this done, try again:  
[eva@hadoop1 ~]$cd /usr/hadoop/hadoop-2.2.0-src  
[eva@hadoop1 ~]$mvn package -Pdist,native -DskipTests -Dtar  

we got error here:  
_[INFO] ------------------------------------------------------------------------  
[ERROR] Failed to execute goal org.apache.hadoop:hadoop-maven-plugins:2.2.0:protoc (compile-protoc) on project hadoop-common:   org.apache.maven.plugin.MojoExecutionException: 'protoc --version' did not return a version -> [Help 1]  
[ERROR]  
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.  
[ERROR] Re-run Maven using the -X switch to enable full debug logging.  
[ERROR]  
[ERROR] For more information about the errors and possible solutions, please read the following articles:  
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/  MojoExecutionException  
[ERROR]  
[ERROR] After correcting the problems, you can resume the build with the command  
[ERROR]   mvn <goals> -rf :hadoop-common_
  
we need to install  
[eva@hadoop1 ~]$cd /usr/protobuf-2.5.0  
[eva@hadoop1 ~]$./configure  
[eva@hadoop1 ~]$make  
_bash: make: command not found_  
(http://blog.163.com/ppy2790@126/blog/static/10324224120136280347690/)  
[eva@hadoop1 ~]$yum -y install gcc automake autoconf libtool make  
[eva@hadoop1 ~]$make  
[eva@hadoop1 ~]$make check  
[eva@hadoop1 ~]$make install  
[eva@hadoop1 ~]$vi /etc/profile  
_export PROTOC\_HOME=/opt/protobuf-2.5.0  
export PATH=$PATH:$PROTOC\_HOME/src  
source /etc/profile_  

after this done, try again:
[eva@hadoop1 ~]cd /usr/hadoop/hadoop-2.2.0-src
[eva@hadoop1 ~]mvn package -Pdist,native -DskipTests -Dtar

Finally, we successed:

_[INFO] ------------------------------------------------------------------------  
[INFO] BUILD SUCCESS  
[INFO] ------------------------------------------------------------------------  
[INFO] Total time: 22:32.025s  
[INFO] Finished at: Fri Feb 07 02:43:59 GMT 2014  
[INFO] Final Memory: 137M/747M  
[INFO] ------------------------------------------------------------------------  
[root@hadoop1 eva]#cd /usr/hadoop/hadoop-2.2.0-src/hadoop-dist/target_  

and now you'll see the target

