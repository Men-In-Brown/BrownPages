---
toc: True
comments: True
layout: post
title: Debugging Event
description: Using debugging tools in LocalHost to demonstrate knowledge on common API errors
type: tangibles
permalink: /debugging-event
---

# Pre-Summary

I will be demonstrating my knowledge of:
- Debugging
- Breakpoints in:
    - Frontend fetch()
    - Authentication in ```MvcConfig.java```
    - console.log() in frontend login
- CORS error
- 401 error
- CORS & 401 through the Network view

> Note: Username is ```toby@gmail.com```, password ```123toby```.

# Debugging & Breakpoints

### Frontend

Since we are trying to induce errors with login, we need to go to our Login page and add breakpoints at the fetch() statements like so:

<img src="{{ site.baseurl }}/images/frontend-breakpoints.png">

### Backend

Similarly, we need to make breakpoints in the backend. Since we want to debug the login, and this login page uses JWT tokens to authenticate users, we need to add a breakpoint to the authentication code in the JWT API controller, in ```JwtApiController.java```, like so:

<img src="{{ site.baseurl }}/images/backend-breakpoints.png">

# CORS Error

A CORS error will occur if the backend does not map the frontend to its registry. The backend's CORS registry determines what sites can get their requests to the backend accepted or not. If we want to trigger a CORS error, we simply have to remove the frontend from the registry. To do that, we can alter the URL in the CORS mapping method like so:

<img src="{{ site.baseurl }}/images/corsmapping.png">

Now when we try to log in, we get this:

<img src="{{ site.baseurl }}/images/corserror.png">

# 401 Error

401 is defined by the internet as an access-denied error. When a user tries to log in, but their credentials are not valid as a user, the site will throw an error reporting that access to data has been denied. We don't really have to do much to the code in the backend, we can just type in some wrong credentials. As seen in the login, I have entered a wrong email and a wrong password, and it has thrown an error accordingly.

<img src="{{ site.baseurl }}/images/401error.png">

# Extra: Networking Panel

Another way to see errors is through the networking panel. Below is an example of an error (the 401 error previously mentioned) seen through the networking panel:

<img src="{{ site.baseurl }}/images/networkingerror.png">