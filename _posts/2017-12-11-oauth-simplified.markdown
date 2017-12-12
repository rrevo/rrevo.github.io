---
layout: post
title:  Understanding OAuth
date:   2017-12-11
---

[OAuth](https://en.wikipedia.org/wiki/OAuth) is an [open standard](https://tools.ietf.org/html/rfc6749) for sharing access to data stored on one application with another application without revealing passwords. With this simplified problem statement, I'll explain how OAuth works.

# Actors

The actors in this story are-

* User - This is an actual person.
* Resource Server (RS) - Application which has some User information or data that is needed by some other application.
* Client Server (CS) - Another application that needs access to data from Resource Server.

# Overview

Let's assume that the Resource Server has some information that is available at GET https://resource-server.com/api/users/info. Now this information is needed from the Client server which can make a REST call to RS at the url above. But this information is only visible to authorized clients. However we do not want to share the user password for RS with CS. Instead an access_token is shared with CS that will be accepted by RS.

Given a sufficiently complex string, the probability of guessing the access_token is pretty low and is secure enough for most applications.

With the OAuth flows, the problem becomes securely getting an access_token from the RS to the CS after confirmation from the User.

# Flows

Various interactions between the actors are shown below

<!--
"User"->"Client Server": Action

par User Login
  "Client Server"->"Client Server": "Generate state"
  User->"Resource Server": /login info with client state and id
  "Resource Server"->"Client Server": /redirect_uri with Code
end

par Token Exchange
  "Client Server" ->"Resource Server": /token send code, client_id, client_secret
  "Resource Server"->"Client Server": Token response
end

par Resource API calls
  "Client Server"->"Resource Server": /api with access_token header
  "Resource Server->"Client Server" : Resource response
end
-->

<img src="{{ site.url }}{{ site.baseurl }}/imgs/oauth.png" />

## Client Server setup

Before the actual User OAuth flows happen, the Client Server is setup once with the Resource Server. A client_id and client_secret are configured between the two applications. This information is never sent to the Users (via mobile apps or browsers) but is always exchanged and validated directly between the RS and CS in the Token Exchange phase.

The Client Server is configured with the resource server authorization and token URLs.
The Resource Server can optionally be configured with redirect URLs of the Client server for the flow after User login.

## User Initialization

* The user performs some action on the client server where access to information from the resource server is needed.
* This triggers the User Login flow on the client server.

## User Login

* The Client Server generates a random value called state. This value will need to be validated later.
* The Client Server opens the Resource Servers authorization URL with the parameters of state, client_id, redirect_uri.
* The Resource Server internally saves the client state and prompts the User to grant access to the Client Server. A login might be needed
* After successful login by the User, the RS generates a temporary token called code. 
* The RS invokes the redirect_uri URL on the CS with parameter state sent before login and code.
* The CS validates that the state parameter is the same that it sent earlier to prevent spoofing. State should be a one time use token with limited expiration.

## Token Exchange

* With the code, the Client Server can now request an access_token.
* Client Server makes a call to the Resource Server token URI. Parameters passed are code, client_id and client_secret.
* The Resource server validates the parameters passed and issues a token to the Client Server.
* The client_secret is known only to both the servers. The code is a token that should expire after some time and cannot be reused.

## Resource API calls

The final step is the Client Server getting access to some data from the Resource Server. Using curl, the access_token is passed as part of a header like-

`curl -H "Authorization: Bearer access_token" https://resource-server.com/api/users/info`

The RS can then serve the resource after validation of the access_token.

# Home automation examples

## Client Server - Alexa and Google Home

[Alexa]({{ site.url }}{{ site.baseurl }}/2017/05/14/alexa-homeskill/) and Google Home are examples of OAuth client servers. Both these are voice controlled clients. For integration, your application needs to act as a Resource Server and grant access to some User information or device that needs to be controlled.

The OAuth aspect is saving client information and implementing login and token URLs. Both Alexa and Google Home have defined a protocol to access resources as well.

## Resource Server - Nest

[Nest](https://developers.nest.com/documentation/cloud/architecture-overview) is an example of a Resource Server. Devices and home information known by Nest can be read and controlled by alternate clients. For integration with Nest, your application needs to act like a Client Server in the flows above. The have good documentation on [OAuth](https://developers.nest.com/documentation/cloud/authorization-overview) configuration.

# More scenarios

## Refresh tokens

Access_tokens usually expire after a short period of time. Another type of token known as the refresh_token can be sent to the client which has a longer lifetime. Using the refresh_token it is possible to get new access_tokens which can be used for API calls.

## Grant types

The process explained here is specific to the grant type known as authorization_code. Here both servers can privately communicate and a client_secret can be exchanged. There are also other grant types like authorization code, implicit, resource owner password
   credentials, and client credentials.

## Scopes

The OAuth protocol allows for limited access of resources via scopes. So rather than access to all the data for a user, only limited subsets can be available based on the scope that the user allows.

## Resource Server vs Authorization Server

In the specification, the Resource Server is decomposed into the Resource Server that has the resource endpoints and the Authorization Server that has the OAuth flow endpoints. Both applications are controlled by the same team. For simpler deployments, the RS and AS can be the same server with different REST endpoints.

# Hope

At first glance, OAuth looks pretty hard but it's not as complicated to implement.

