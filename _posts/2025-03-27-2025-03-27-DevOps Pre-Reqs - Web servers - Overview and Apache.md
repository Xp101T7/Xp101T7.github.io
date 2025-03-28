---
title: 2025-03-27-DevOps Pre-Reqs - Web servers - Overview and Apache 
date: 2025-03-27 12:00:00 -500
categories: [Web Servers,Apache]
tags: [Apache,Apps,Web Server,Devops]

---

> [!quote] Make the best use of what is in your power, and take the rest as it happens.
> — Epictetus

---

## 2025-03-27-DevOps Pre-Reqs - Web servers - Overview and Apache 

## Application Types

- Desktop
- Mobile
- Web
- Analytics
- Problem Solving

There are standalone like calc.exe and client-servers like discord chat servers, There are different kinds of servers

- Chat Server
- Web Server
- Application Server
- DB Server
- Email Servers
- Backup Servers


Web and app servers

Web is front end ui and images and orders are processed its the application server. In small scale could serve both in small apps. 

In big apps there will be multiple servers not just web and app that are linking themselves. 

---

## Web Servers

In a web app the machine running the webapp is the server and the browser is the client. 

Server side code or backend code is requested JAVA, Python, JS. The response is front end usually, html, CSS, javascript. Both are written together. 


springboot framework we can define mappings for http calls like /product calls to return products from the DB. 

Then the Jquery client side code sends back this info. 

python or nodejs web frameworks are the same for being able to receive requests and respond. python flask can run the web server code natively but its not recommended. The web server hosts the application the code can be run on. The code doesn't need to be in the OG format but it can be in a package like with Java. 

Apache Tomcat, NGINX, gunicorn are examples of servers that can host web servers, the server can run one or more processes that listen on a specific port.  Apache 8080, NGINX = 80, Gunicorn = 8000 by default.  Web servers can host multiple apps at the same time. 


## Static and Dynamic Websites

A site containing only pics that don't change is a static website. 

Ecommerce though is dynamic because it often has a backend to process purchases and orders. But will also contain static content alongside the processing payment code. 




