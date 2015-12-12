---
layout:     post
title:      "How to creata a simple application using waylay API"
date:       2015-12-12 10:19:06
categories: UI
---

With waylay you can create very complex rules using simple [template] [rest]. In order to create a rule and initiaite a task, you need to do define:
* sensors and actuators, 
* conditions under which actuators need to be executed
* relations between sensors and gates
* task definition (periodic, onetime, cron, reactive).
For more info, please check the documentation

We have create a small boostrap/jquery app that shows how you can create an application on top of waylay. Application is available in [github] [repo].
![UI application]({{ site.baseurl }}/assets/images/application.png)

In order to use it, you will need a waylay account and you will need to login to this application using your API keys.
We also added three more features that are similar to our [labs website] [labs]:
* allow you to push runtime data
* allow you to simulate data from the csv file
* show you how to use our cloud cache in combinataion with freeboard

You can also access our live here: [demo] [demo]

[repo]: https://github.com/waylayio/demo-hvac
[rest]: http://docs.waylay.io/Waylay-REST-API-documentation.html#Createthetask
[labs]: http://labs.waylay.io/
[freeboard]: https://freeboard.io/
[demo]: http://demo-customers.waylay.io/

