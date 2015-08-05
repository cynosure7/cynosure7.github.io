---
date: 2015-07-27
layout: post
title: Java EE Exercise
category: tech
tags: JavaEE
---
##Requirement
Write an application that provides CRUD (create, read, update, delete) RESTful services for customer data. 
Each customer has a  
- name  
- address  
- telephone number  

The code should be production level quality and it should be possible to run the application.   
Further, the code should demonstrate your level of proficiency in TDD/BDD.

The technology to be used:  
-Java 7 or later  
-free choice of Java frameworks
##Architecture and technologies
This exercise implementation uses a 3-layered architecture:  
![architecture](/images/java/architecture.png)  
--RESTful API layer (Jersey): exposes RESTful API to clients.  
--Business layer (Spring): where the business logic happens.  
--Persistence layer (Hibernate): communicates with the MySql database.
##Resource
As the requirement defines, a customer will have the following properties:  
id - generated uniquely identifies the customer  
name - customer's name  
phone - customer's phone number  
address - customer's address
##REST API
###Create a Customer
URI: /customers  
Method: POST  
Body: {  
"name": "Jack Johns",  
"phone":"021 3221 0921",  
"address":"72 Nelson St., Auckland"  
}  
Response: 201(Created), 409(Conflict), 400(Bad Request)
###Get a Customer
URI: /customers/{id}  
Method: GET  
Response: 200(OK), 404(Not Found)
###Update a Customer
URI: /customers/{id}  
Method: PUT  
Response: 200(OK), 400(Bad Request)
###Delete a Customer
URI: /customers/{id}  
Method: PUT  
Response: 200(OK), 404(Not Found)
##Logging
Log requests and responses via Logback.
log file is in ./customer.log (daily rotated logs are in ./archieve)
##Exception
Implemeted CustomerException and ErrorMessage; map it to responses.
##Testing
Test-driven Development (Junit + Mockito).  
Intergrating test with SoapUI.
##Security
Basic backend input validation.  
Hibernate parameter binding.
##ToDo
Protect API with https, OAuth 2.0.