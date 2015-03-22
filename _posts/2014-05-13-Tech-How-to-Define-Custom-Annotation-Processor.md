---
date: 2014-05-16 16:12:00+00:00
layout: post
title: How to Define a Custom Annotation Processor
category: tech
tags: Annotation Java Eclipse
---

##What's Problem?
Now we have a new Automated Testing Framework, and we are adding cases to it. What we need is the number of cases we have added to it. These cases have case ID and some descriptions, we can use case ID to identify these cases in our code, by custom annotation, but how to get them?

```java
 @CaseIDs(values = {
        @CaseID(id="fun3204"),
        @CaseID(id="fun3205"),
        @CaseID(id="fun3209"),
        @CaseID(id="smo1289"),
    })
    public void testClient(String caseID, String clientID) throws Exception{
        logger.info("Entering "+ caseID + " as the first line...");
        //...
        if(actualResponse.getStatusCode() == RestConstants.HTTP_OK_200){
            //...
            }
        } else{
            //...
        }
        logger.info("Leaving "+ caseID + " as the last line...");
    }
```

##How to deal with it?
We can use ADT to implement this feature. APT stands for Annotation Processing Tool. Sun shipped an API for APT in JDK 1.5. In JDK 6, the API was modified considerably and standardized through JSR-269, and the Sun implementation was built into the javac compiler rather than requiring a standalone tool.

So we can write our own annotation processor to process these annotations. Here are some reference:  

[Introduction to Annotation Processing in Eclipse](http://www.eclipse.org/jdt/apt/introToAPT.php "Introduction to Annotation Processing in Eclipse")  

[http://kerebus.com/2011/02/using-java-6-processors-in-eclipse/](http://kerebus.com/2011/02/using-java-6-processors-in-eclipse/ "Using Java 6 processors in Eclipse")

And bellowing is my code:

[Annotation-Processing-Tool](https://github.com/cynosure7/Annotation-Processing-Tool)