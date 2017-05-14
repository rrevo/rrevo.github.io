---
layout: post
title:  Alexa Smart Home integration
date:   2017-05-14
summary:
tags:   alexa
---

Alexa is Amazon's voice search. It is integrated into the Echo line of devices. Alexa can be extended by implementing a "skill". The [SDK](https://developer.amazon.com/alexa-skills-kit) has details on how this can be done.

## Skills

Broadly there are 3 types of skills-
* Custom skills - Any type of speech pattern can be used for integration. However users need to say a keyword to identify the skill.
* Smart Home skills - These are Alexa specific speech patterns for [smart home integration](https://developer.amazon.com/public/solutions/alexa/alexa-skills-kit/docs/smart-home-skill-api-reference). Skills cover lighting, door locks and thermostats. 
* Flash Briefing skill - For quick information like news.

## OAuth

[OAuth](https://oauth.net) is an authorization framework that enables a third-party application to obtain limited access to an HTTP service. This allows cross account integration between the alexa/amazon login account and your services account. OAuth is mandatory for Smart Home skills and optional for Custom skills. Amazon has good documentation on [Account Linking](https://developer.amazon.com/public/solutions/alexa/alexa-skills-kit/docs/linking-an-alexa-user-with-a-user-in-your-system). 

The 4 roles for use in OAuth are resource owner, resource server, client, and authorization server. For Alexa integration, these become-

* Resource owner: The end user who wants to connect your skill with their user account in your system.
* Resource server: The server hosting a service.
* Client: This is the Alexa service.
* Authorization server: The server that authenticates the identity of the resource owner and issues access tokens.

### Flows

OAuth Authentication Flow is-

* In the Alexa app, the user enables the skill.
* The Alexa app shows the Authorization URL provided when registering your skill on the developer portal. When the Alexa app calls this URL, it includes state, client_id, response_type, scope, and redirect_uri as query string parameters.
    * The state is used by the Alexa service during the account linking process. Your page needs to keep track of this value, as you must pass it back later.
    * The client_id is a value defined by you when you set up account linking for the skill in the developer portal.
    * The response_type is always ```code``` for the code grant flow.
    * The scope is an optional list of access scopes indicating the level of access requested. You define the set of scopes to support when enabling account linking for your skill.
    * The redirect_uri is the URL to which your service redirects the user once the user is authenticated.
* The user logs in using their normal credentials for your site.
* Your service authenticates the user and then generates a code.
* Your service redirects the user to the specified redirect_uri and passes along the state and code in the URL query parameters.


OAuth Token Flow is-

* Alexa then invokes the Access Token URI with the code, and client secret.
* If valid, the service should return a OAuth spec json response like-

```
{
  "access_token": "secret-token",
  "refresh_token": "uber-refresh-token",
  "expires_in": 3600
}
```

* The user’s Alexa account is now linked to the account in your service, and the skill is ready to be used.
* The access_token will be sent by Alexa when making API calls to your service.
* When the access_token is expired, the refresh_token will be used to generate a new access_token.

## Smart Home Skills

[Smart Home Skills](https://developer.amazon.com/public/solutions/alexa/alexa-skills-kit/overviews/understanding-the-smart-home-skill-api) are used for basic home automation. There are two phases in using home skills-

* Discovery - After account linking, Alexa has to discover the various devices in the home.
* Commands - This is when the user invokes a command to perform an action.

The [API Reference](https://developer.amazon.com/public/solutions/alexa/alexa-skills-kit/docs/smart-home-skill-api-reference) lists the various commands that can be sent.

## Sample implementation

On [Github](https://github.com/rrevo/alexa-homeskill) I've uploaded a sample implementation with dummy OAuth and a Smart Home skill. The implementation is written in Java and the steps to get a working solution are listed below.

For deployment, the services used are Alexa, API Gateway and Lambda. Use US East (N. Virginia) for deployments in AWS. Alexa is available in that region along with all the other services.

### Build Code

* Clone https://github.com/rrevo/alexa-homeskill
* ```mvn install```
* The jar, target/homeskill-*-jar-with-dependencies.jar will be used.

### Deploy OAuth

* Create a new API in [API Gateway](https://console.aws.amazon.com/apigateway/home?region=us-east-1) called OAuth.
* Create a new [Lambda function](https://console.aws.amazon.com/lambda/home?region=us-east-1#/functions?display=list). Sub steps are-
    * Blank function blueprint
    * API Gateway is the trigger. Select OAuth as the API name.
    * Configure function. name is authorize. Use java 8 runtime. Upload the jar from above. Handler is io.github.rrevo.alexa.homeskill.OAuthAuthorize::handleRequest and role should be lambda_basic_execution.
* After confirmation, a new resource should appear in the OAuth API in the API Gateway.
    * The new resource will have and Integration Request of type Lambda proxy. An important step is to change the Method Request Auth from IAM to None.
* Create a new Lambda function called token similar to the steps above. The handler should be io.github.rrevo.alexa.homeskill.OAuthToken::handleRequest and the jar is the same.
    * Verify that the new resource has been added to the API gateway and change the Method Request.
* Deploy the API in the gateway and copy the URLs for the two resources.

The OAuth lambda functions depend on Lambda proxy integration from the API gateway to be able to set the response codes and headers.

### Create Alexa Skill

Follow steps from [documentation](https://developer.amazon.com/public/solutions/alexa/alexa-skills-kit/docs/steps-to-create-a-smart-home-skill) on creating a smart home skill.

Changes are-
* Use the OAuth urls above for the Authentication URL and Access Token URL. Any client secret can be provided.
* Deploy a new function for the skill. The same jar has the skill implementation. The handler should be io.github.rrevo.alexa.homeskill.SmartHomeSkill::handleRequest.

For the skill, Amazon decided not to follow the OAuth spec. The access tokens are not sent as bearer tokens but part of the payload. I suspect that this is since they wanted to force you to use lambda for deployment. Lambda theoretically allows them to have better control over the alexa user experience.

### Testing

* Use a rest client to test the various OAuth endpoints.
* For Alexa testing, add an account for beta usage. During discovery, a test device called Room Lights is added.
* Say "Alexa, turn on Room lights" to pseudo turn on the lights or "Alexa, turn off Room lights" to turn them off.

# Summary

Using a host of AWS services it is possible to deploy a test Smart home skill. Remember that this is a test application with no security.
