---
layout:  post
title:   AWS API Gateway
date:    2016-01-11
summary: Manage APIs
---

API Gateway is one of the offerings by AWS. It's part of the Application Services group of services and is used to Build, Deploy and Manage RESTful APIs.

The API Gateway is architected to be the public facing API endpoint for some backend services. The backend services can be other EC2 services, Lambda or other public APIs.

The service provides related sub-services. Some features include-

* Caching
* Monitoring and analytics - Cloudwatch logs
* API Throttling
* Transformation of payload
* SDK generation for some platforms
* Stages for versioning
* Security

I've prepared a quick introductory presentation [here](https://github.com/rrevo/aws-apigateway) which covers basic usage and features.

It's interesting how AWS has moved into various IaaS, PaaS and SaaS services. Google App Engine had come up with many PaaS APIs a long time back. At that time I was concerned about lock-in and stayed away. Now with the large ecosystem and deployed services that AWS commands, customers are already locked-in. So just another service that greatly simplifies deployment is more palatable.
