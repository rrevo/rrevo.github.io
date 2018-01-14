---
layout: post
title:  Smart Home integration with Google
tags:   home-automation google-home
---

## Google ecosystem

[Google Home](https://store.google.com/product/google_home) is a voice controlled speaker from Google. These devices are direct competitors to the Alexa product line from Amazon. Products from other companies can integrate with Google home to be controlled via voice.

[Google Assistant](https://assistant.google.com) is another product from Google that allows for text or voice actions across google products. Voice via a home device just becomes an alternate input method for Google Assistant.

[Smart home integration](https://developers.google.com/actions/smarthome/) with Google is essentially writing a Google Assistant extension. However both the actual products and developer documentation are pretty confusing because of this Home vs Assistant split. In this blog I'll just cut through the process specifically for a smart home application.

## Development

### Create project

[Actions on Google](https://console.actions.google.com/) is the platform for creating new Google Assistant extensions.

Create a new project at the [main site](https://console.actions.google.com/u/0/).

* Select "Create Smart Home action" from the template options.

![Create Project]({{ site.url }}{{ site.baseurl }}/imgs/google-home-createproject.png)
* Select a name and location for the project.

![Create Action]({{ site.url }}{{ site.baseurl }}/imgs/google-home-createaction.png)
* Specify a fulfillment URL. This will be the REST endpoint which will respond to the home requests. More below.

![Action Information]({{ site.url }}{{ site.baseurl }}/imgs/google-home-action-information.png)
* Specify other App information like name, description, images etc.
* Account Linking is for OAuth integration which is a requirement.
* Finally hit "Test Draft" when you are ready to start testing.

### OAuth

The next phase of the project is to get OAuth integration working. Similar to Amazon Alexa, your service is going to be the Resource Server. See my [OAuth simplified](/2017/12/11/oauth-simplified/) blog for specifics.

Google also has good documentation on [OAuth](https://developers.google.com/actions/identity/oauth2-code-flow)

For testing of OAuth, there is a web tool at called [OAuth Playground](https://developers.google.com/oauthplayground/). Click on the settings icon on the top right to expose the options for OAuth testing.

![OAuth testing]({{ site.url }}{{ site.baseurl }}/imgs/google-home-oauth-playground.png)

The endpoints will be those of your services along with client Ids and secrets.

When a user tries to integrate with the smart home action, the user is first asked to login to the service as per the OAuth flows and the access token is then stored for future interactions.

### REST endpoint

Google will invoke a single REST endpoint for all interactions. The documentation for home integration is [here](https://developers.google.com/actions/smarthome/).

For all requests the OAuth token is going to be sent in the Authorization request header in the form "Bearer $TOKEN" which can be used by the service to get the user information for the request.

Request body is encoded in json and depending on the type of request, various parts will need to be parsed for a valid response.

There are 3 request types that need to implemented. These are called "intents" in google action terminology.

* [action.devices.SYNC](https://developers.google.com/actions/smarthome/create-app#actiondevicessync) - Return the device metadata that the user can control.
Each device had attributes like an identifier, name, nicknames. Device type (like Light or Washer) sets the the icons in the Google Assistant app.
Actual features supported by the device are specified by the traits. Some example traits are [OnOff](https://developers.google.com/actions/smarthome/traits/onoff), RunCycle, Brightness, ColorSpectrum. A light might allow for dim controls which is [Brightness](https://developers.google.com/actions/smarthome/traits/brightness) but not support changing colors like [ColorSpectrum](https://developers.google.com/actions/smarthome/traits/colorspectrum).
Based on the traits of a devices, various interactions cause EXECUTE requests.
* [action.devices.EXECUTE](https://developers.google.com/actions/smarthome/create-app#actiondevicesexecute) - When the user wants to actually do something with a device, an EXECUTE command is sent. Like turning a device On or Off. Based on the command, parameters might also be sent, like the brightness level for dimming a light.
* [action.devices.QUERY](https://developers.google.com/actions/smarthome/create-app#actiondevicesquery) - Query is used to get the current state of the device. This API seems to be optional from the documentation but is required as part of the final submission process.

This part of the API is well documented and easy to follow. In addition you need to make sure to handle error cases which can be command specific or general like OAuth failures.


### Testing and submission

* Basics of testing is documented [here](https://developers.google.com/actions/smarthome/testing-deploying)
* [Validator](https://developers.google.com/actions/smarthome/tools/validator/) is useful for checking the SYNC Json payload.
![OAuth testing]({{ site.url }}{{ site.baseurl }}/imgs/google-home-permissions.png)
* You can initially target specific users for testing. The Permissions link above, shows the IAM console in the google cloud platform where you can assign access. "Project View" permissions are required for testers.
* Once you are ready for releasing to the wide world, Publishing is the next step.
* In the actions overview page, the "Submit Draft for Review" starts the approval process with Google to get your action approved. 
* There might be additional requirements and steps after submission for approval beyond what is listed on the site. Documentation does lie sometimes.

## Thoughts

* The Google Home API is very similar to the Alexa v3 specification. I am not sure which team came up with the design first.
* From a consumer point of view, Google has an extremely confusing interface. Right now I am supposed to install their Home app for setup and also the Assistant app for actual interaction.
* I like typing commands in Google Assistant in addition to the voice interaction.
* Google competes very well with Amazon in this space. They are pricing their devices pretty low like Amazon.
* Alexa now has routines which I think of as ifttt competition. Google does not seem to have a similar interface right now.
* Apple homekit is completely losing it as of now. [Homepod](https://www.apple.com/homepod/) is expensive and time is being lost. It's the appstore game that Apple won with ios and they are definitely late now.
* Where is microsoft in this space?
* Will it be possible to have privacy centric devices that listen for API commands only on the local lan/wifi? I do not care about IoT devices over "dumb devices" without a strong reason for why they should be internet connected. With many IoT devices there is a loss of simplicity which I find worse rather than the advantages of being online.

Home and voice are definitely going to be massive in the next few years. Only professionals will want laptops and other explicit computing devices. For most people, seamless computing by talking to your devices or using your phone are going to be the future. The operating system (like windows or macos) are going to be irrelevant; rather the future is going to be a platform like alexa or google.
