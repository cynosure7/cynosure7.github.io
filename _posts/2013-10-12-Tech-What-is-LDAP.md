---
date: 2013-10-12
layout: post
title: What is LDAP?
category: tech
tags: LDAP
---
##What is LDAP
LDAP stands for Lightweight Directory Access Protocol. It is an application protocol used over an IP network to manage and access the distributed directory information service. The primary purpose of a directory service is to provide a systematic set of records, usually organized in a hierarchical structure. It's similar to a telephone directory that contains a list of subscribers with their contact number and address. In order to commence an LDAP session, a client needs to connect to the server known as the Directory System Agent, which is set by default to use TCP port 389. After the connection is established, the client and server exchange packets of data. Basic encoding rules are used to transfer information between the server and client.

##Structure of LDAP
Although the structure of LDAP seems relatively complex, it is fairly simple to understand. A set of attributes is contained in any entry. Each attribute accommodates one or more values, and has a name. Each entry in the directory is assigned a unique identifier that consists of a Relative Distinguished Name. The server is capable of holding a sub-tree and its children, beginning from a particular entry.

##Operations on LDAP
There are plethora of operations that can be performed on the Lightweight Directory Access Protocol.

**Bind** - On connection with the LDAP server, the default authentication state of the session is anonymous. There are basically two types of LDAP authentication methods - the simple authentication method and the SASL authentication method.

**ADD** - This is used to insert a new entry into the directory-to-server database.

**Delete** - As the name suggests, this operation is used to delete an entry from the directory. In order to do this, the LDAP client has to transmit a perfectly composed delete request to the server.

**Modify** - This operation is used by LDAP clients to make a request for making changes to the already existing database. The change to be made must be one of the following operations:

1. Add(including a new value).
2. Delete (deleting an already existing value).
3. Replace (Overwriting an existing value with a new one).

**Unbind** - This is the inverse of the bind operation. Unbind aborts any existing operations and terminates the connection, leaving no response in the end.

##Reference
http://www.youtube.com/watch?v=di5hZZ8Ty8g

http://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol
