---
date: 2014-09-24 20:29:00+00:00
layout: post
title: RESTful-API-Implementation-With-Jersey
category: Tech
tags: RESTful API Jersey
---
#Background
We have a new requirement comes from other team in our project. They need a API to delete user's tokens when they delete one user. This makes sense since we do not need to keep a user's tokens once it's deleted. Although the tokens can be deleted by our clean up job when they expire, but the refresh token's expire time is quite long now. So to make our data clean we need a new API which delete tokens by given user id.

#API Design
##What should this API be like?
As you see in the title, we need design and implement a RESTful API. What does RESTful mean? To be honest, I'm not really sure either (Are you kidding me?). Anyhow, we should come up one and then improve it.
##level 0
```
POST /tokensService HTTP/1.1  
[various other headers]  

<DeleteRequest userId = "user1-123"/\>
```

The server then will response this information. This design is using HTTP as a transport system for remote interactions, but without using any of the mechanisms of the web.
```
HTTP/1.1 200 OK  
[various headers]
```
This is a straightforward design, and it's simple.
##level 1 - Resource
Here we will introduce resources. Rather than making all our requests to a singular service endpoint, we now start talking to individual resources.

So with our initial query, we might have a resource for given userId.
```
POST /tokens/userId/user1-123 HTTP/1.1  
[various other headers]  

<DeleteRequest/\>
```

The difference is that if anyone needs to do anything about user's tokens, they first get hold of the user resource and post to that resource. Rather than calling functions in one general API with arguments, we call a method on one particular object.
##level 2 - HTTP Verbs
In level 0 and level 1, I used HTTP POST verbs for all interactions, but some people use GETs instead or in addition. At these levels it doesn't make much difference, they are both being used as tunneling mechanisms allowing you to tunnel your interactions through HTTP. Level 2 moves away from this, using the HTTP verbs as closely as possible to how they are used in HTTP itself.
```
DELETE /tokens/userId/user1-123 HTTP/1.1
Host: helloowen.com
```
In this level, the API becomes very close to real experience. It defines what you want to do to a resource.
##What is REST?
REST is an architectural style which is based on web-standards and the HTTP protocol. 
#Reference
https://jersey.java.net/

http://code.tutsplus.com/tutorials/a-beginners-guide-to-http-and-rest--net-16340

http://www.vogella.com/tutorials/REST/article.html#rest_overview

http://martinfowler.com/articles/richardsonMaturityModel.html

