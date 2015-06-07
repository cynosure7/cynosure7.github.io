---
date: 2014-09-13
layout: post
title: My Git Practice (to be continued...)
category: tech
tags: Git
---
##Background
Our team switched from SVN to Git in June this year.

##My git practice in work
Below are some Git problems I encountered in my work. The solutions to these questions are from Internet. The solutions list here may not be the only or best one, there may be other better solutions.
###How to synchronize fork?
How to synchronize your fork to keep it up-to-date with the upstream repository?  

I forked a project and contributed some features in  my separated repository. At the same time, the project also got some changes, so now my fork is not updated with the main project. How can I update my fork with the main project?
###Solution:
By design, forking a project creates a separated repository which is not updated when the original repository changes. However, we can do it manually and it's pretty easy.

What we have is 3 repository:

1. **Upstream**: the upstream project's repository on Github
2. **Origin**: my fork's repository on Github
3. **Local**: my local repository on my laptop.  
(cloned by using `git clone git@github.company.com:myfork/project.git`)

Steps:

1. Add the upstream project as a remote:  
`git remote add upstream git@github.company.com:main/project.git`
2. Pull changes from upstream remote master branch into my local repository master branch:  
`git pull upstream master`
3. Now my local repository is synchronized with upstream, then push my local repository to my fork:  
`git push origin master` 

###How to solve merge conflict?  
When I merge with master branch, conflict comes (in fact, it's very common). I need to resolve these conflicts manually.
###Solution:
When conflict occurs, we need to open the conflicted files, and they look like this:

```java
class ConflictDemo
{
    public static void main(String args[])
    {
<<<<<<< HEAD
        String versionControl = "SVN";
=======
        String versionControl = "Git";
>>>>>>> master
        System.out.println(versionControl);
    }
}
```
A conflict-marked area begins with `<<<<<<<` and ends with `>>>>>>>`. These are also known as the conflict markers. The two conflicting blocks themselves are divided by a `=======`. The first section is the version of the current branch head. The second section is the version of master branch.

We have several options here. We can either keep our changes, take our coworker's changes, or make a brand new change. (Whatever you do, you need to make sure to resolve the conflict such that the file makes sense, and everyone is happy.)
After we edit the code and resolve this conflict, we can now git add this file, commit the change with a new commit message.

`git add ConflictDemo.java`  
`git commit -m "Merged master fixed conflict."`
###How to change file permissions in Git on Windows?  

My develop environment is windows 7. After I created a build.sh file and pushed it to original, but the project's build failed: `./build.sh: Permission denied`. The cause is this build.sh doesn't have execute permission. So I need to add +x permission to it, on windows?
###Solution:
As we know, Git manages file permissions for each file in the repository, so we need to have the executable bit set for shell/bash files to make them executable in Linux System. But on windows, file permissions do not map to the Git file permissions, so it may be a bit hard to change the file permissions. Here are the steps:

1. check current file permission:  
`C:\Git\myProject>git ls-tree HEAD`  
_`100644 blob 55c0287d4ef21f15b97eb1f107451b88b479bffe    build.sh`_
2. change it to 755:  
`C:\Git\myProject>git update-index --chmod=+x build.sh`
3. commit our change:  
`C:\Git\myProject>git commit -m "Changing file permissions"`  
_`[master 77b171e] Changing file permissions 0 files changed, 0 insertions(+), 0 deletions(-)
 mode change 100644 => 100755 build.sh`_
4. check permission again:  
`C:\Git\myProject>git ls-tree HEAD`  
_`100755 blob 55c0287d4ef21f15b97eb1f107451b88b479bffe    build.sh`_
5. push  
`git push origin master`

###How to convert file to UNIX text file format
When I build my project, I encountered this error:  
`./build.sh: line 34: syntax error: unexpected end of file`
The reason may be mismatched structure - that is, you do not have matching double quotes, matching single quotes, have not closed a control structure such as a missing `fi` with an `if`, or a missing done with a `for`. Or you wrote/edited it on a Windows machine and transferred it to a Linux machine (in Linux, `\n` writes a newline while for Windows it is`\r\n`). And the cause of my problem it the later one.
###Solution
Switch to a Linux Environment and edit this file and then push it. In Linux we can use command `dos2unix build.sh` (in CentOS, if the command is not found, `yum install dos2unix` and try again) to convert it from DOS/MAC to UNIX text file format.

##Reference
[1] How can I Resync a fork from original http://superuser.com/questions/456145/how-can-i-resync-a-fork-from-original

[2] RESOLVING CONFLICTS http://githowto.com/resolving_conflicts

[3] How to change file premissions in Git on Windows http://blog.lesc.se/2011/11/how-to-change-file-premissions-in-git.html