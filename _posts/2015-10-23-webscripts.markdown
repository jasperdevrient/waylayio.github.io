---
layout:     post
title:      "webscripts, or how to hack LoRa, Sigfox in 20 seconds"
date:       2015-12-23 10:19:06
categories: integration
---

Recently we discovered [webscripts] [webscripts]. It is really great, it lets you run lua code in the cloud. Note that lua scripts allow you to use general settings, similar to waylay, but also allow you to pass input arguments and forms. That also means that you can create a webhook from another system, that posts data into the webhooks, and then inside the lua script call a POST method towards the other system. What does it mean? You can actuall with HTTP GET call a REST POST to another system. For some people this might sound as a heresy, but if for instance you need to do a payload transforamtion before calling our broker, this might be all you need. Since LoRa and Sigfox are low volume notifications, you might as well be good with this even in production. So let's see one webscript that goes to our bridge: 

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


[webscripts]: https://www.webscript.io
