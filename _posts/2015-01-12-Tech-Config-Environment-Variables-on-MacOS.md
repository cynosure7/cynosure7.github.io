---
date: 2015-01-12
layout: post
title: Config Environment Variables on MacOS
category: tech
tags: MacOS
---

##Background
I just bought a Macbook, it's pretty cool. To enable installed tools' command I need to set encironment variables, and I find a neat way to get it done.
##Steps
1. ```sudo nano /etc/paths```
2. Enter your password, then you'll get this ![nano](/images/macos/nano.png)
3. Go to the bottom of the file and enter the path you wish to add.
4. Press 'control'+'x' to exit, and it will ask you if you want to save the change: ![quit](/images/macos/quit.png)
5. Press 'Y' to save the file ![Y](/images/macos/Y.png)
6. Press 'Enter' to confirm.

Done!
Now open a new terminal window and ```echo $PATH``` you'll find the path has been there.