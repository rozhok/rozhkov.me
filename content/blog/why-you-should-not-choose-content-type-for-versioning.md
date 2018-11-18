+++
author = "Vladimir Rozhkov"
draft = true
tags = ["tech"]
categories = ["tech"]
description = ""
date = "2017-03-11T22:21:54+02:00"
title = "Why you shouldn't choose Content-Type header for API versioning"
disqus = true
+++

Long time ago CTO asked me to design our API versioning scheme.

I have to do some research for existing approaches, take cons and pros and came up with decision. So I do that, and probably as you know, there is at least 3 approaches to API versioning:

- Widely-used and adopted URL-based versioing. You just add `/v1` prefix and that's it. There a number of REST evangelists who say that this violates REST principles, but many of companies use this scheme: twitter/google/shopify
- Query-param versioning. You have to add `?version=1.0` to your request.
- Header-based versioning, like `X-API-VERSION` header or, more commonly used `Content-Type` header. You just pass version in each of yours requests and that's it.

Our main framework for that time was Spring, so keeping this fact in mind, I've tried to chose better way which 

>Example of class with `@RequestMapping`

>Problems involved 