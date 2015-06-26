---
date: 2014-04-23
layout: post
title: How to Use logrotate to Rotate Log by Both Data and Size
category: tech
tags: Tomcat Log Rotation
---
##Problem
We have some log files in different directories, and they are not rotated. About the logs in apache-tomcat, though they rotated through date, there are two problems. The first is the catalina.out continues to grow despite of daily rotated catalina logs, and the other is these logs are not rotated by size.
##Design
We want the log can be rotated like this:

Access.2014-03-31.log  
Access.2014-03-31.log.1  
Access.2014-03-31.log.2  
Access.2014-03-31.log.3  

Firstly, we rotate the log files through the date, and then rotate the log file when file size reaches a specific size (default 100 MB), and the old log file is renamed by appending a "1" to the name. Each time a new log file is started, the numbers in the file names of old log files are increased by one, so the files "rotate" through the numbers. Old log files whose number exceeds a threshold will then be deleted.
##Implementation
###Apache-tomcat log rotation
Firstly, according the official document, we need to do some configuration(<a href="http://tomcat.apache.org/tomcat-6.0-doc/logging.html#Considerations for productive usage" target="_blank">Considerations for productive usage</a>):

- Consider removing ConsoleHandler from configuration. 
By default (thanks to the .handlers setting) logging goes both to a FileHandler and to a ConsoleHandler. The output of the latter one is usually captured into a file, such as catalina.out. Thus you end up with two copies of the same messages. (That is to say, daily catalina logs and the catalina.out have the same messages, so catalina.out keeps growing)


- Consider removing FileHandlers for the applications that you do not use. E.g., the one for host-manager.


- The handlers by default use the system default encoding to write the log files. It can be configured with encoding property. See Javadoc for details.


- Consider configuring an <a href="http://tomcat.apache.org/tomcat-6.0-doc/logging.html#Access_logging" target="_blank">Access log</a>.

####How to rotate logs through file size
Tomcat has in default rotation through date, and it works well, if we need size rotation, we may need use logrotate (more from <a href="http://www.rackspace.com/knowledge_center/article/understanding-logrotate-part-1" target="_blank">here</a>). logrotate is designed to ease administration of systems that generate large numbers of log files.

Logrotate Config Steps:  

1. Create file 'tomcat' (or other names you like) in directory /etc/logrotate.d  (That's where most of the application-specific configuration files are)

2. Copy the following contents to the file you created:(for details of these parameters, you can refer to <a href="http://linuxcommand.org/man_pages/logrotate8.html" target="_blank">this</a>.)  
/usr/local/apache-tomcat/logs/*.log {  
    copytruncate  
    rotate 5  
    missingok  
    size=500M  
}

3. In default, logrotate runs daily, because it is located in /etc/cron.daily

But if we need to rotate log through size, run daily may be too slow, so we can move logrotate to /etc/cron.hourly

###How to use logrotate to implement log rotation through both date and size
I create two logrotate task, the first one is for size and the other one for data. The task for size will be run every 5 miniutes and the task for date will be run in the middle night. Both this task will be configure in user's cron job.

Belowing is the sample shell code:


	function configLogrotate() {
		echo 'begin to config logrotate...'

		touch $BASE_DIR/taskBySize
		echo -e "/opt/apache-tomcat/logs/*.log /opt/apache-tomcat/logs/*.out /opt/apache-tomcat/logs/*.txt /opt/debug/*.log /opt/log/*.log {\ncopytruncate\nrotate 10\nmissingok\nsize 500M\n}" > taskBySize

		touch $BASE_DIR/taskByDate
		echo -e "/opt/config/idb/*/*.log.1 /opt/config/idb/*/*.log.2 /opt/config/idb/*/*.log.3 /opt/config/idb/*/*.log.4 /opt/config/idb/*/*.log.5 /opt/config/idb/*/*.log.6 /opt/config/idb/*/*.log.7 /opt/config/idb/*/*.log.8 /opt/config/idb/*/*.log.9 /opt/config/idb/*/*.log.10 {\n    dateext\n    daily\n    nocopytruncate\n    rotate 5\n    missingok\n    nocreate\n}\n/opt/identity/broker/current/identityBroker/config/idb/*/*.log {\n    dateext\n    daily\n    copytruncate\n    rotate 5\n    missingok\n}  " > taskByDate

		if [ ! -f $BASE_DIR/cronjob ];then
			touch $BASE_DIR/cronjob
			echo -e "59 23 * * * /usr/sbin/logrotate $BASE_DIR/taskByDate\n* * * * * /usr/sbin/logrotate $BASE_DIR/taskBySize" > cronjob
			crontab cronjob
		else
			echo "cronjob has been created"
		fi

		echo 'finished config logrotate...'
	}
