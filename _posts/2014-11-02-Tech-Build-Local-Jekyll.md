---
date: 2014-11-02
layout: post
title: Install Jekyll in Mac OS X Yosemite
category: tech
tags: Jekyll
---

##Background
In the past, I didn't have a local test environment and whenever I update my bolg I'll commit my changes to github directly. The disadvantage of this way is that my blog may doesn't work or sometimes shows in an odd style. In addition to this, there are so many messy commits in my github. But now I put this blog in my resume, so more guys can visit it. To make my blog stable, it's necessary to build a local Jekyll to test my updates. Recently I bought a mackbook pro, so I'll install a local Jekyll in Mac OS(10.10 Yosemite).
##Steps
###Install gcc
Apple now provides Command Line Tools for Xcode for OS X Lion and later version. You can download it from [Apple's developer site](https://developer.apple.com/downloads/index.action) (free registration required and search for "Command Line Tools"). For me, I download Command Line Tools (OS X 10.10) for Xcode - Xcode 6.1. If you've installed Xcode which contains command line tools, you can skip this step. If not, you may have [this problem](http://stackoverflow.com/questions/11462367/cant-install-jekyll-on-mac-trying-to-set-up-github-blog).
###Install jekyll
`sudo gem install Jekyll`
###cd your blog
Now cd to you local folder, for me it's
`cd HelloOwen`
###Start jekyll serve
`jekyll serve`
###Browse it
Browse to http://localhost:4000 and you will get your blog. Now it's quite easy for you to update you blog and verify your change before you push it to github. It's quite easy, right?


##Reference
http://stackoverflow.com/questions/11462367/cant-install-jekyll-on-mac-trying-to-set-up-github-blog

https://github.com/kennethreitz/osx-gcc-installer

https://developer.apple.com/downloads/index.action#