---
title: "Servlet 과 Servlet Container"
date: 2021-05-09 08:00:00 -0400
categories: java
---

> Reference
>
> https://dzone.com/articles/what-servlet-container

# Servlet 과 Servlet Container

web server receives http request
web server forwards the request to servlet container
the servlet is dynamically retrieved and loaded into the address space of the container, if it is not in the container.
the container invokes the init() method of the servlet for initialization(invoked once when the servlet is loaded first time)
the container invokes the service() method of the servlet to process the http request, i.e., read data in the request and formulate a response. the servlet remains in the container’s address space and can process other http requests.
web server return the dynamically generated results to the correct location
