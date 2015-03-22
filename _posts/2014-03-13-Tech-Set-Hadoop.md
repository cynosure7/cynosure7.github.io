---
date: 2014-03-13 22:36:00+00:00
layout: post
title: How to Set Hadoop 2.2.0 in CentOS (64bit)
category: tech
tags: Hadoop CentOS 64bit
---

##Precondition: install jdk in hadoop2 and hadoop3  

**[eva@hadoop1 ~]$ ssh-keygen -t rsa**  

_Generating public/private rsa key pair.
Enter file in which to save the key (/home/eva/.ssh/id\_rsa):  
Created directory '/home/eva/.ssh'.  
Enter passphrase (empty for no passphrase):  
Enter same passphrase again:  
Your identification has been saved in /home/eva/.ssh/id\_rsa.  
Your public key has been saved in /home/eva/.ssh/id\_rsa.pub.  
The key fingerprint is:  
aa:2e:69:10:ee:d7:f9:b0:a0:e4:2f:ac:f4:45:4c:2c eva@hadoop1  
The key's randomart image is:_  
`+--[ RSA 2048]----+
|                 |
|    .            |
|   E o           |
|.   +            |
|..   o  S        |
|..  .  .         |
|o+ o.oo          |
|++*.o+o          |
|o++=o...         |`

**[eva@hadoop1 ~]$ cd /home/eva/.ssh**  
**[eva@hadoop1 .ssh]$ ll**  
_total 8  
-rw------- 1 eva eva 1671 Feb  7 05:02 id\_rsa  
-rw------- 1 eva eva  393 Feb  7 05:02 id\_rsa.pub_  

**[eva@hadoop1 .ssh]$ cp id\_rsa.pub authorized\_keys**

**[root@hadoop1 .ssh]$ vi /etc/hosts**  
_127.0.0.0         localhost     localhost  
10.224.126.120     hadoop1       hadoop1  
10.224.126.121     hadoop2       hadoop2  
10.224.126.122     hadoop3       hadoop3_
  
**[root@hadoop1 .ssh]$ source /etc/hosts**  

**[eva@hadoop1 .ssh]$ scp authorized\_keys hadoop2:/home/eva/.ssh/**  
**[eva@hadoop1 .ssh]$ scp authorized\_keys hadoop3:/home/eva/.ssh/**  

**[eva@hadoop2 .ssh]$ chmod 644 authorized_keys**  
**[eva@hadoop2 .ssh]$ ls -la**  
_total 16  
drwx------ 2 eva eva 4096 Jan 10 02:59 .  
drwx------ 3 eva eva 4096 Jan 10 03:03 ..  
-rw-r--r-- 1 eva eva  393 Feb  7 05:18 authorized\_keys  
-rw-r--r-- 1 eva eva  808 Jan 10 02:59 known\_hosts_  

**[eva@haoop3 ~]$ cd /home/eva/.ssh**  
**[eva@haoop3 .ssh]$  chmod 644 authorized_keys**  
**[eva@haoop3 .ssh]$ ls -la**  
_total 12  
drwx------ 2 eva eva 4096 Jan 10 02:56 .  
drwx------ 3 eva eva 4096 Jan 10 02:56 ..  
-rw-r--r-- 1 eva eva  393 Feb  7 05:18 authorized\_keys_  

**[eva@haoop3 ~]$ ssh hadoop2**  
**[eva@haoop3 ~]$ ssh hadoop3**  

turn off iptables  
**[eva@hadoop1 ~]$ sudo su**  
**[root@hadoop1 eva]# chkconfig iptables off**  

**[eva@hadoop1 .ssh]$ mkdir -p ~/dfs/name**  
**[eva@hadoop1 ~]$ mkdir -p ~/dfs/data**  
**[eva@hadoop1 ~]$ mkdir ~/temp**  


**[eva@hadoop1 ~]$ cd /usr/hadoop/hadoop-2.2.0-src/hadoop-dist/target**  


**[eva@hadoop1 ~]$ cp -r hadoop-2.2.0 /home/eva**  
**[eva@hadoop1 ~]$ cd ~**  
**[eva@hadoop1 ~]$ ll**  
_total 16  
drwx------ 4 eva  eva  4096 Feb  7 06:55 dfs  
drwx------ 9 root root 4096 Feb  7 07:14 hadoop-2.2.0  
drwx------ 2 root root 4096 Jan 26 05:33 java  
drwx------ 2 eva  eva  4096 Feb  7 06:56 temp_  

**[root@hadoop1 eva]# chown -R eva hadoop-2.2.0/**  

**[root@hadoop1 eva]# chown -R eva java**  
**[root@hadoop1 eva]# ll**  
_total 16  
drwx------ 4 eva eva  4096 Feb  7 06:55 dfs  
drwx------ 9 eva root 4096 Feb  7 07:14 hadoop-2.2.0  
drwx------ 2 eva root 4096 Jan 26 05:33 java  
drwx------ 2 eva eva  4096 Feb  7 06:56 temp_  


**[eva@hadoop1 ~]$ cd /home/eva/hadoop-2.2.0/etc/hadoop**  

**[eva@hadoop1 hadoop]$ vi hadoop-env.sh**  

_\# The java implementation to use.  
export JAVA\_HOME=/usr/java/jdk1.7.0\_51_  

**[eva@hadoop1 hadoop]$ vi yarn-env.sh**  

_\# some Java parameters  
\# export JAVA\_HOME=/home/y/libexec/jdk1.6.0/  
export JAVA\_HOME=/usr/java/jdk1.7.0\_51_  

[eva@hadoop1 hadoop]$ vi slaves  
hadoop2  
hadoop3  

**[eva@hadoop1 hadoop]$ vi core-site.xml**  
`<property>
    <name>fs.defaultFS</name>
    <value>hdfs://hadoop1:9000</value>
</property>`

`<property>
    <name>io.file.buffer.size</name>
    <value>131072</value>
</property>`

`<property>
    <name>hadoop.tmp.dir</name>
    <value>file:/home/eva/tmp</value>
    <description>Abase for other temporary directories.</description>
</property>`

`<property>
    <name>hadoop.proxyuser.eva.hosts</name>
    <value>*</value>
</property>`

`<property>
    <name>hadoop.proxyuser.eva.groups</name>
    <value>*</value>
</property>`

**[eva@hadoop1 hadoop]$ vi hdfs-site.xml**  
`<property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>hadoop1:9001</value>
</property>`

`<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/home/eva/dfs/name</value>
</property>`

`<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/home/eva/dfs/data</value>
</property>`

`<property>
    <name>dfs.replication</name>
    <value>3</value>
</property>`

`<property>
     <name>dfs.webhdfs.enabled</name>
     <value>true</value>
</property>`

**[eva@hadoop1 hadoop]$ cp mapred-site.xml.template mapred-site.xml**  
**[eva@hadoop1 hadoop]$ vi mapred-site.xml**  
`<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>`

`<property>
    <name>mapreduce.jobhistory.address</name>
    <value>hadoop1:10020</value>
</property>`

`<property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>hadoop1:19888</value>
</property>`

**[eva@hadoop1 hadoop]$ vi yarn-site.xml**  
`<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>`

`<property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>`

`<property>
    <name>yarn.resourcemanager.address</name>
    <value>hadoop1:8032</value>
</property>`

`<property>
    <name>yarn.resourcemanager.scheduler.address</name>
    <value>hadoop1:8030</value>
</property>`

`<property>
    <name>yarn.resourcemanager.resource-tracker.address</name>
    <value>hadoop1:8031</value>
</property>`

`<property>
    <name>yarn.resourcemanager.admin.address</name>
    <value>hadoop1:8033</value>
</property>`

`<property>
    <name>yarn.resourcemanager.webapp.address</name>
    <value>hadoop1:8088</value>
</property>`

###copy to other notes  

**[eva@hadoop1 ~]$ scp -r hadoop-2.2.0/ eva@hadoop2:~/**  
**[eva@hadoop1 ~]$ scp -r hadoop-2.2.0/ eva@hadoop3:~/**  

##setup validation:  
###format namenode:  
**[eva@hadoop1 ~]$ cd ~/hadoop-2.2.0/**  
**[eva@hadoop1 hadoop-2.2.0]$ ./bin/hdfs namenode -format**  
###start:  
**[eva@hadoop1 hadoop-2.2.0]$ ./sbin/start-all.sh**  

**[eva@hadoop1 ~]$ jps**  
_19093 ResourceManager  
16196 NameNode  
19442 Jps  
16364 SecondaryNameNode_  

**[eva@hadoop2 ~]$ jps**  
_3192 DataNode  
4399 Jps  
4247 NodeManager_  

**[eva@hadoop3 ~]$ jps**  
_1426 DataNode  
2178 Jps  
2026 NodeManager_  

##run example:  
**[eva@hadoop1 hadoop-2.2.0]$ cd /home/eva**  

**[root@hadoop1 eva]# wget http://www.gutenberg.org/cache/epub/20417/pg20417.txt**  

**[eva@hadoop1 ~]$ cd hadoop-2.2.0/**  
**[eva@hadoop1 hadoop-2.2.0]$ bin/hdfs dfs -mkdir /tmp**  

**[eva@hadoop1 hadoop-2.2.0]$ bin/hdfs dfs -copyFromLocal /home/eva/pg20417.txt /tmp**  

**[eva@hadoop1 hadoop-2.2.0]$ bin/hdfs dfs -ls /tmp**  
_Found 1 items  
-rw-r--r--   3 eva supergroup     674570 2014-02-08 06:16 /tmp/pg20417.txt_

**[eva@hadoop1 hadoop-2.2.0]$ bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.2.0.jar wordcount /tmp/pg20417.txt /tmp-output**  
_Exception in thread "main" org.apache.hadoop.mapred.FileAlreadyExistsException:   Output directory output already exists_  
Solution:  
**[eva@hadoop1 hadoop-2.2.0]$ bin/hadoop fs -rmr /tmp-output**

If you formate the namenode for times, you may meet a error:http://f.dataguru.cn/forum.php?mod=viewthread&tid=32712&fromuid=10801

meet problem:Path is not a file: /tmp/hadoop-yarn  

**[eva@hadoop1 hadoop-2.2.0]$ bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.2.0.jar wordcount /tmp/pg20417.txt /tmp-output**  
_14/02/08 07:50:52 INFO client.RMProxy: Connecting to ResourceManager at hadoop1/10.224.126.120:8032  
14/02/08 07:50:53 INFO input.FileInputFormat: Total input paths to process : 1  
14/02/08 07:50:53 INFO mapreduce.JobSubmitter: number of splits:1  
14/02/08 07:50:53 INFO Configuration.deprecation: user.name is deprecated. Instead, use mapreduce.job.user.name  
14/02/08 07:50:53 INFO Configuration.deprecation: mapred.jar is deprecated. Instead, use mapreduce.job.jar  
14/02/08 07:50:53 INFO Configuration.deprecation: mapred.output.value.class is deprecated. Instead, use mapreduce.job.output.value.class  
14/02/08 07:50:53 INFO Configuration.deprecation: mapreduce.combine.class is deprecated. Instead, use mapreduce.job.combine.class  
14/02/08 07:50:53 INFO Configuration.deprecation: mapreduce.map.class is deprecated. Instead, use mapreduce.job.map.class  
14/02/08 07:50:53 INFO Configuration.deprecation: mapred.job.name is deprecated. Instead, use mapreduce.job.name  
14/02/08 07:50:53 INFO Configuration.deprecation: mapreduce.reduce.class is deprecated. Instead, use mapreduce.job.reduce.class  
14/02/08 07:50:53 INFO Configuration.deprecation: mapred.input.dir is deprecated. Instead, use mapreduce.input.fileinputformat.inputdir  
14/02/08 07:50:53 INFO Configuration.deprecation: mapred.output.dir is deprecated. Instead, use mapreduce.output.fileoutputformat.outputdir  
14/02/08 07:50:53 INFO Configuration.deprecation: mapred.map.tasks is deprecated. Instead, use mapreduce.job.maps  
14/02/08 07:50:53 INFO Configuration.deprecation: mapred.output.key.class is deprecated. Instead, use mapreduce.job.output.key.class  
14/02/08 07:50:53 INFO Configuration.deprecation: mapred.working.dir is deprecated. Instead, use mapreduce.job.working.dir  
14/02/08 07:50:53 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1391845037760_0003  
14/02/08 07:50:54 INFO impl.YarnClientImpl: Submitted application application_1391845037760_0003 to ResourceManager at hadoop1/10.224.126.120:8032  
14/02/08 07:50:54 INFO mapreduce.Job: The url to track the job: http://hadoop1:8088/proxy/application_1391845037760_0003/  
14/02/08 07:50:54 INFO mapreduce.Job: Running job: job_1391845037760_0003  
14/02/08 07:51:02 INFO mapreduce.Job: Job job_1391845037760_0003 running in uber mode : false  
14/02/08 07:51:02 INFO mapreduce.Job:  map 0% reduce 0%  
14/02/08 07:51:09 INFO mapreduce.Job:  map 100% reduce 0%  
14/02/08 07:51:17 INFO mapreduce.Job:  map 100% reduce 100%  
14/02/08 07:51:17 INFO mapreduce.Job: Job job_1391845037760_0003 completed successfully  
14/02/08 07:51:17 INFO mapreduce.Job: Counters: 43_  

        File System Counters  
                FILE: Number of bytes read=267026  
                FILE: Number of bytes written=692433  
                FILE: Number of read operations=0  
                FILE: Number of large read operations=0  
                FILE: Number of write operations=0  
                HDFS: Number of bytes read=674670  
                HDFS: Number of bytes written=196192  
                HDFS: Number of read operations=6  
                HDFS: Number of large read operations=0  
                HDFS: Number of write operations=2  
        Job Counters  
                Launched map tasks=1  
                Launched reduce tasks=1  
                Data-local map tasks=1  
                Total time spent by all maps in occupied slots (ms)=5064  
                Total time spent by all reduces in occupied slots (ms)=5651  
        Map-Reduce Framework  
                Map input records=12760  
                Map output records=109844  
                Map output bytes=1086547  
                Map output materialized bytes=267026  
                Input split bytes=100  
                Combine input records=109844  
                Combine output records=18040  
                Reduce input groups=18040  
                Reduce shuffle bytes=267026  
                Reduce input records=18040  
                Reduce output records=18040  
                Spilled Records=36080  
                Shuffled Maps =1  
                Failed Shuffles=0  
                Merged Map outputs=1  
                GC time elapsed (ms)=74  
                CPU time spent (ms)=4410  
                Physical memory (bytes) snapshot=397582336  
                Virtual memory (bytes) snapshot=1758060544  
                Total committed heap usage (bytes)=355467264  
        Shuffle Errors  
                BAD_ID=0  
                CONNECTION=0  
                IO_ERROR=0  
                WRONG_LENGTH=0  
                WRONG_MAP=0  
                WRONG_REDUCE=0  
        File Input Format Counters  
                Bytes Read=674570  
        File Output Format Counters  
                Bytes Written=196192

###look word count result： 
**[eva@hadoop1 hadoop-2.2.0]$ bin/hadoop fs -cat /home/hadoop/test/test_wordcount/out/\***  
