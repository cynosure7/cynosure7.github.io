---
date: 2014-07-20
layout: post
title: What is OAuth 2.0? - My Understanding OAuth 2.0
category: tech
tags: OAuth-2.0
---
##Background
I've worked in Cisco OAuth 2.0 Team for more than one year. If my friends or colleagues from other teams asked me, what are you working on recently? I said, OAuth 2.0. And the question later must be, what's that. And I'll explain it to them; sometimes it's a little difficult because they are not engineers work on related fields. So I want to write an article to explain this.

Well, this is yet another article about the OAuth 2.0. As an industry-standard open standard for authorization used by many companies today, OAuth 2.0 has already been the subject of a lot of articles. On the day this sentence was written, a search on Google for keyword about "OAuth 2.0" returned about 1,780,000 results. So why does there need to be another? Firstly, this article is to give the overview of OAuth 2.0, it does not contain or rely on any specific implementation detail, and it try to explain my understanding OAuth 2.0 in simple word. It's for both programmers who want to call API (Application Programming Interface) which is protected by OAuth 2.0 and normal users who are resource owners approve the authorization.

I hope this article can help others who touch OAuth 2.0 at the first time.
Please point out any mistake in this article and welcome your comments.

##What is OAuth 2.0?
What's that? Well, not everybody has heard that, but for sure, most netizens experienced it. In a word, OAuth is a protocol with which a 3-party app can access your data stored in another website without your account and password. For more official definition, you can refer to Wiki or specification. 

Here is a use case demo:

1. I logged in LinedIn and want to connect some friends who are in may Gmail contact. And fortunately LinkedIn supports this, so I clicked this button:  
![Add Connection](/images/pics/AddConnection.png)

2. A web page pop up, and it shows the Gmail log in page, when you enter your account and password.  
![Add Connection](/images/pics/loginpage.png)

3. An consent page shows and I clicked 
![Add Connection](/images/pics/AuthorizationPage.png)

4. Now LinkedIn can access my contacts in Gmail:
![Add Connection](/images/pics/result.png)

##How it works
Showing below is the flow chart of the case above.

![Add Connection](/images/pics/OAuth.png)

Step 1: LinkedIn request a token to Authorization Server.

Step 2: The authorization server authenticates the resource owner and show user the consent page. (If the user is not log in Google, he/she needs to log in firstly)

Step 3: User grants the LinkedIn's access request.

Step 4: the authorization server responds back with an access token.

Step 5: LinkedIn call Gmail API with this access token.

Step 6: Gmail resource server returns your contacts if the access token is valid. (The token will be verified by resource server)

##Terminology
In the world of OAuth 2.0, there are four roles that are defined in the standard:

1. Resource Owner  
An entity capable of granting access to a protected resource. When the resource owner is a person, he is referred to as an end user.
2. Resource Server  
The server that hosts the protected resource, capable of accepting and responding to protected resource requests using access tokens. The resource can be anything, for example, resume on LinkedIn.
3. Client  
An application that makes protected resource requests on behalf of the resource owner and with his/her authorization. The term `client` does not imply any particular implementation characteristics (for example, whether the application executes on a server, a desktop, or other device).
4. Authorization Server  
The server that issues access tokens to the client, after successfully authenticating the resource owner and obtaining authorization.

##Benefits
So what are the benefits of OAuth 2.0? What if we have no OAuth 2.0?

Before OAuth, the default mechanism to access protected API was to leverage something that each site had for user - his password. The so called _password anti-pattern_ allowed Site A to use an API hosted by Site B to access a user's attributes and data by asking his password at Site B. By demonstrating knowledge of the user's password on subsequent API calls, Site A would prove to Site B that it had the user's authorization to access the data.

This pattern has some following deficiencies:

1. It teaches users to be indiscriminate with distributing their passwords (a habit that phishing ultimately relies on).
2. As the hosting site is not involved in the authorization step, the hosting site is unable to provide to the user a record of which requesting sites he has authorized to access his data held at the hosting site. the burden is on the user to track such grant.
3. The copies of the passwords at the requesting sites present a risk for breach through compromise.
4. It doesn't support granular permissions, e.g. Site A can read but not write. Because it relies on the requesting site impersonating the user, the hosting site must grant the same privileges to the requesting site as to the users themselves.
5. It doesn't support (easy) revocation, to be sure of turning off the access rights previously granted to a requesting site, users must change their password at the hosting site. If they had previously authorized other requesting sites at the same hosting site, changing the password would immediately (presumably unintentionally) revoke those permissions, as well.
6. Because it relies on passwords to the hosting site being distributed across the Web, it effectively locks that site into password authentication, preventing it from adopting stronger or federated alternatives (without negatively impacting users).

##History
One thing you might notice is that I add version 2.0 after OAuth, so this means there is 1.0 or other version. There are 3 versions so far:

OAuth 1.0

OAuth 1.0a

OAuth 2.0(RFC 6749)

###Key Difference Between 1.0 and 2.0
The key difference between 2.0 and 1.0 is that OAuth 1.0 is based on having shared secrets between the server and consumer which are used to calculate signatures. Those signatures are then used to verify the authenticity of API requests, while OAuth 2.0 removes signatures and instead relies on SSL to secure the secret. You can get more details from this [questions](http://stackoverflow.com/questions/4113934/how-is-oauth-2-different-from-oauth-1).

###About OAuth 1.0a
>In 2009, an attack on OAuth 1.0 was identified which relied on an attacker initiating the OAuth authorization sequence, and then convincing a victim to finish the sequence- a result of which would be the attacker's account at an (honest) client being assigned permissions to the victim's resources at an (honest) RS. OAuth 1.0a was the revised specification version that mitigated the attack.

##Business Value
Now that we have a rough idea of the purpose of OAuth 2.0, let's look at how it fits into the business puzzle and why it is important to you and your organization.

More and more frequently, organizations are exposing their own APIs to attract third party developers who can take advantage of the organization's services to build apps and widgets, with the ultimate goal of driving top-line revenue. Companies like Amazon, Netflix, Sears, etc. do this to attract intermediaries who can connect buyers with themselves as sellers, and create convenient ways to transact.

For this strategy to work, however, a certain level of trust must be established between the parties involved. Identity is an important piece of this puzzle and it is imperative that organizations secure the exposure of services via APIs. OAuth 2.0 is a component of any Identity Relationship Management (IRM) platform, and addresses these security issues and provides a convenient way to deal with authorization.

From a developer perspective, (described by Ryan Boyd at Google), before the dawn of OAuth 2.0 inside Google, developers spent 80% of their time dealing with authorization in new applications. The ability to deal with authorization in a simpler, standardized way cuts costs, saves money, and brings applications to market faster.

##Something Notable
###Non-standard Implementation and Deployment
You might have heard some vulnerabilities about OAuth 2.0, e.g. Cover Redirect found by Wang Jing. In fact, most of the vulnerabilities do not reside within the protocol itself, but rather its implementation and deployment. The root cause of Cover Redirect is the validation of redirect_uri. Some implementations do not validate the redirect_uri passed from the request or just a simple validation (part of, not a full match). OAuth 2.0 Threat Model and Security Considerations has pointed out some related threats and given countermeasures, e.g. [Open Redirectots on Client](https://tools.ietf.org/html/rfc6819#section-4.1.5) and [Malicious Client Obtains Authorization](https://tools.ietf.org/html/rfc6819#section-4.4.1.4). An attacker could gain access to authorization codes or access tokens. To counter these threats, the authorization server should validate the client's redirect URI against the pre-registered redirect URI (completely match is required), if one exists.  And the examples like this go on and on, for example, TLS/SSL is the recommended approach to prevent any eavesdropping during the data exchange, but many implementations does not require this. So when you want to implement and deployment OAuth 2.0, you probably have some security experts on site to figure it all out for you.

###Application Developers are not Disciplined
OAuth security relies on highly disciplined application developers. Of course, developer should not request unnecessary permissions, but in most cases, they break this promise. Under normal circumstances, usually 90% application we use only need read-only access, but instead, only 5% of the applications request read-only privileges.

For developers, trying to get permission to use the user account seems to be a kind of trend. Some application developers have the app ask for far more permissions than the app actually needs. These requests are plainly presented to the user at the consent page, and the user has to approve them or abort the app.

###Unsuspecting Resource Owners
Almost without fail the user gives approval for the app to access virtually every aspect of the resource, just to get the app to work. That's if the user even pays attention to the permissions being requested, many don't particularly care. They just hit the button and get on with the app work.  

Those are the permissions that the LinkedIn app requested to run. Yes, LinkedIn can read and write to my personal contact data and can use my credentials for any account on my system. Worse, it can delete my entire contacts, because I told it that's OK.

Sure LinkedIn is a well-known service so it's no problem. But think back to when LinkedIn was just getting started. The LinkedIn app was asking for those permissions then, too, and we were all saying OK. The fact is unless an app is questionable to begin with we are all just going to approve any permission it asks for. We want to get on with using the app and that's that.

It is bad for apps to do things behind the user's back, so asking permission should be mandatory. Even if that happens, we are our own worst enemy and we'll end up approving just about anything it wants to do, even if it sounds fishy.

The next time you authorize an application, pay particular attention to the permissions it is asking you to give it. Take control over your info.

##Reference
http://forgerock.com/oauth2/

https://wikis.forgerock.org/confluence/display/openam/OpenAM+OAuth+Implementation

http://designingsocialinterfaces.com/patterns/The_Password_Anti-Pattern

http://openam.forgerock.org/openam-documentation/openam-doc-source/doc/admin-guide/index/chap-oauth2.html

http://docs.media.bitpipe.com/io_10x/io_100664/item_426600/The-Essentials-of-OAuth.pdf

https://www.pingidentity.com/en/resources/articles/oauth.html

http://stackoverflow.com/questions/7561631/oauth-2-0-benefits-and-use-cases-why

http://ravirajsblog.blogspot.com/2010/10/why-use-oauth-what-are-benefits.html

https://tools.ietf.org/html/rfc6819

http://hueniverse.com/2012/07/26/oauth-2-0-and-the-road-to-hell/