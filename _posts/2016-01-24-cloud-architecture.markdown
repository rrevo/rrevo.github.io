---
layout:  post
title:   Cloud Web Application Architecture
date:    2016-01-24
summary: Reference architecture for new deployments
---

AWS has some great [Reference architecture diagrams](https://aws.amazon.com/architecture/) for common Applications.

## Web applications


[Web application](http://media.amazonwebservices.com/architecturecenter/AWS_ac_ra_web_01.pdf) is probably the most common architecture which consists of front end web servers, backend API servers and persistence. This is shown below-

![Web Architecture]({{ site.url }}{{ site.baseurl }}/imgs/aws-ref-arch.png)

### Key Aspects

* The User gets the DNS of the service and invokes the Cloudfront service.
* Cloudfront and Object store(S3) usage are used for static content.
* For dynamic content, the Load Balancer fronting the webservers is invoked.
* Cloudfront is optional and so the Load Balancers can directly be exposed.
* The Load Balancer routes requests to the webservers. The cloud provides easy dynamic scale up and down of webserver instances based on load.
* For API requests, there is another backend pool of servers fronted by another load balancer.
* The webservers invoke the API servers via the secondary load balancer.
* These API servers can also scale dynamically.
* The API servers can access a data repository. Unless you require nosql usecases and are not at crazy scale, start out with a traditional database.

## Microservice architecture

For more complex application requirements, you can switch to microservices. Microservices (like other decisions) has a lot to do with team management and not only code. The architecture of an application follows the organizational structure of the company or team. With microservices you are able to move independent pieces of the product independently. There is definitely an increased cost in complexity.

The talk [Multi-Tenant Application Deployment Models](https://www.youtube.com/watch?v=DMP0leGZpo4) explains various deployment models from traditional on-prem to serverless. The Pool Architecture shown below is a Microservices diagram.

![Pool Architecture]({{ site.url }}{{ site.baseurl }}/imgs/pool-arch.png)

### Key aspects

* One entire Web Application becomes a Service. There are multiple services that are composed together for the complete Web Application.
* Each service can have dependencies on other services and they talk to each other via a public (within the scope of other services and not necessarily end user-facing) API.
* Each service has its own private repository. Other services can use only the service API and not directly access the internal repository models.
* The user inteface can be composed in the backend at a higher layer by invoking these services.
* The services should not be exposed via the public Load balancer.
* A public API that is versioned can be exposed.

### And...

* The various web and API servers follow the "[shared nothing](https://en.wikipedia.org/wiki/Shared_nothing_architecture)" architecture. Each instance in a server pool can service a request. This allows for horizontal scaling.
* State is maintained only in the persistence layer. Backup, replication and other services need to be set up.
* Context information about the user, tenant is cascaded from the top layers to the bottom layers.
* [Distributed Tracing](http://research.google.com/pubs/pub36356.html) is required to trace remote calls.
* Discovering services, handling error cases when composing service calls are other concerns.
