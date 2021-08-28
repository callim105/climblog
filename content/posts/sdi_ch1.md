---
title: "System Design Interview: Ch1. Scale from Zero to Millions of Users"
date: 2021-08-15T22:08:54-05:00
draft: true
---

## Overview
The first chapter of this book describes building a simple system
that supports a single user, which is then scaled up to serve
millions of users.

## Single Server Setup
Since this chapter is meant to start simple, everything is 
run on a single server. This includes the web app, database,
cache, etc.

To understand the setup, it's helpful to know the request flow
and traffic.

### Request Flow 
1. Users access websites through domain names.
2. IP address is returned to the browser or mobile app
3. Once the IP is obtained HTTP requests are sent directly to
your web server.
4. The web server returns HTML or JSON.

Now let's take a look at where the traffic is coming from.

### Traffic Source
In this instance, the traffic to the web server comes from two 
sources: a Web Application and a Mobile application
- Web App: uses a combo of server-side languages to handle business
logic, storage, etc. and client-side languages for UI.
- Mobile App: Communicates with web server via HTTP. JSON is the
commonly used response format.

## Introducing Multiple Servers
### Database
When the users grow, we're going to need multiple servers. We can
start with one for web/mobile traffice, and one dedicated to the
database. 

{{< admonition type=tip title="scaling tip" open=false >}}
Separating web/mobile traffic and database servers allows them
to be scaled independently!
{{< /admonition>}}

### Choosing a database
So, basically you can decide between using a traditional relational
database or you can choose a non-relational db.

- *Relational Databases*: Also known as SQL databases, these are
pretty common, and store data in tables and rows. Some common 
relational databases are MySQL and PostgreSQL.
- *Non-Relational Databases*: Also known as NoSQL dbs. NoSQL 
databases can further be grouped into 4 categories:
    - key-value stores
    - graph stores
    - column stores
    - document stores
Generally, its hard to do joins on non-relational data.

For most use cases, relational databases work well, but NoSQL
may be a better choice if your app needs super low latency,
you have unstructured data, you only need to serialize/deserialize
data, or you need to store a massive amount of data.

## Vertical scaling vs Horizontal Scaling
**Vertical scaling** is basically adding more power to your servers.
I often think of it like, beef up your existing servers to handle
more.

**Horizontal Scaling** is also referred to as "scale-out". This
lets you scale by adding more servers into your resources.
In the real world vertical scaling is seriously limited. It's 
impossible to add unlimited CPU and memory to a single server, and
a single server also means a single point of failure.

