---
layout:     post
title:      "Integrating Waylay and IFTTT (IF This Then That)"
date:       2015-06-30 14:59:06
categories: integration
---
#IFTTT
> IFTTT is a web-based service that allows users to create chains of simple conditional statements, called "recipes", which are triggered based on changes to other web services such as Gmail, Facebook, Instagram, and Craigslist. https://en.wikipedia.org/wiki/IFTTT

#Integration

In this blog post, you will learn how to connect between Waylay and IF in two separate chapters:
* From Waylay to IF
* From IF to Waylay

#What you need

* An IFTTT account
* A Waylay account: Click the Start a Free Trial button on [our website][waylayio] to request an account.

#From Waylay to IF

##IF Maker Channel

Using IF's [Maker] channel and Waylay's IFTTTMaker actuator we can send a POST request to trigger your IF recipes.
First, you will need to have a recipe on IFTTT ready to receive a web request.
For "IF" channel, choose the Maker Channel and an event name (e.g. waylay_test).
![IFTTT_tut_step2 screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_step2.JPG)
![IFTTT_tut_step3 screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_step3.JPG)

Then pick any "THEN" channel you wish. (e.g. send email)
![IFTTT_tut_step5 screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_step5.JPG)
![IFTTT_tut_step6 screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_step6.JPG)
![IFTTT_tut_step7 screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_step7.JPG)

##Waylay IFTTTMaker Actuator

After logging into your Waylay platform, we'll create a template which consist of a switch sensor and IFTTTMaker actuator.
Enter the eventName (e.g. waylay_test) and the values you wish to have.
![IFTTT_tut_template screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_template.JPG)

We need the IF account's secretKey for the actuator to be able to connect to IF. Find your secret key at [maker] page.
![IFTTT_tut_maker screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_maker.JPG)

And enter the secretKey in the actuator's properties field:
![IFTTT_tut_secretKey screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_prop_secretkey.JPG)

If you haven't already, connect the switch sensor node to the IFTTTMaker actuator node and change the following under advance settings on the actuator node:
Trigger on state change: off -> on

You are now ready to trigger some IF recipes from Waylay!
Simply go under the debug tab and RUN the template! Click on the "Test data" button, and push "on" to switch_1 node.
The result is switch_1 node changing state from OFF to ON and therefore triggering the IFTTTMaker actuator.

#From IF to Waylay

There are so many ways to use Waylay from another application as Waylay provides a huge [REST API]:
You can push data into a resource, create sensors and actuators, create templates, make tasks out of templates, and more via the [REST API]!
In this chapter, we will have two IF recipes pushing data into Waylay broker, and in the end sending us an email notifying us that Apple stock has fallen 5% and is now below $100.

##Creating a template and resource
We will require two sensors in a template, one receiving the closing price and the other receiving the percentage drop in price.
Place two streamDataSensor onto the white space and enter the following properties:
name: streamSensor_stockPrice, parameter: closingPrice_appleStock, threshold: 100
name: streamingSensor_percentageDrop, parameter: percentageDrop_appleStock, threshold: 5
![IFTTT_tut_IFtoWaylay_createTemplate_sensors screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_IFtoWaylay_createTemplate_sensors.JPG)

Then connect both of the sensors to an AND Gate, changing the triggering states to "Below" for price and "Above" for percentage drop.
You will want to connect an actuator at the end of the AND Gate, notifying you of the price and percentage drop.
![IFTTT_tut_IFtoWaylay_createTemplate_ANDGate1 screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_IFtoWaylay_createTemplate_ANDGate1.JPG)
![IFTTT_tut_IFtoWaylay_createTemplate_ANDGate2 screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_IFtoWaylay_createTemplate_ANDGate2.JPG)

You can go ahead and save the template, and then deploy the template as a task.
Do give a unique resource name, because that's what you will be specifying in the IF Maker action channel later on!
![IFTTT_tut_IFtoWaylay_deployTask screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_IFtoWaylay_deployTask.JPG)

##Authentication
Before we proceed further, we will construct the authentication key for the web request to Waylay later.
You will find your Waylay API key and secret under Waylay dashboard-profile-API Key.
![IFTTT_tut_IFtoWaylay_apikeysecret screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_IFtoWaylay_apikeysecret.JPG)

Keep them available as you move on to the IF web application to create recipes.

##Creating IF recipes
We're using the Stocks channel to trigger our Maker action channel in two separate recipes: 
1. price drops below 
2. price drops by percentage
![IFTTT_tut_IFtoWaylay_step2_chooseTrigger screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_IFtoWaylay_step2_chooseTrigger.JPG)
...

For each of them, do the respective settings below:
![IFTTT_tut_IFtoWaylay_step3_pricebelow screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_IFtoWaylay_step3_pricebelow.JPG)
...
![IFTTT_tut_IFtoWaylay_step4_chooseMaker screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_IFtoWaylay_step4_chooseMaker.JPG)
...
![IFTTT_tut_IFtoWaylay_step5_makeWebRequest screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_IFtoWaylay_step5_makeWebRequest.JPG)
...

And finally we are going to configure the Waylay action channel

> ![IFTTT_tut_IFtoWaylay_step4_chooseMaker screenshot]({{ site.baseurl }}/assets/images/IFTTT_tut_IFtoWaylay_step4_chooseMaker.JPG)

To be continued!

#Concluding

Coming soon!

[waylayio]:       https://www.waylay.io/
[waylaydocs]:     https://docs.waylay.io/
[maker]:          https://ifttt.com/maker
[RESTful API]:    http://docs.waylay.io/Waylay-REST-API-documentation.html