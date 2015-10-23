---
layout:     post
title:      "webscripts, or how to hack LoRa, Sigfox in 20 seconds"
date:       2015-12-23 10:19:06
categories: integration
---

Recently we discovered [webscript.io] [webscripts]. It is really a great site, it lets you run lua code in the cloud. Note that lua scripts allow you to use general settings, similar to waylay, but also allow you to pass input arguments and forms. That also means that you can create a webhook from another system, that posts data into the webhooks script (which is webscript in our example), and then inside this lua script you can call a POST method towards the other system. For some people this might sound as a heresy, but if for instance you need to do a payload transformation before calling our broker, this might be all you need. Since LoRa and Sigfox are low volume notifications, you might as well be good with this even in the production. So let's see one webscript that goes to our bridge: 

{% highlight javascript linenos %}
local key = 'WAYLAY_KEY'
local password ='WAYLAY_TOKEN'

local data = {
		temperature = 34,
    		resource = 'deviceX',
    		domain = 'playground.waylay.io'
	}

local response = http.request(
	{
		url='https://data.waylay.io/messages',
		method='post',
		auth =	{	key, password},
	 	data = data
	}).content
{% endhighlight %}

What you can see from here is that the _data_ struct can be indeed comming from another system. In case of LoRa, this is the place where you would do XML transformation. I will check if we are allowed to show the actuall code, so stay tunned!

[webscripts]: https://www.webscript.io
