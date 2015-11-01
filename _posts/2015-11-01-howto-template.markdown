---
layout:     post
title:      "Creating template - best practices"
date:       2015-11-01 10:19:06
categories: integration
---

With our visual designer tool you can debug and create new templates [link] [doc]. Using waylay application portal, or via REST calls [link] [rest] you instantiate these templates as _tasks_. For instance, if you create a template that gathers smart meter data and sends mail with consumption every Friday, you will instantiate this template for every newly provisioned meter. In this post I will show you the easiest way to create a template that will allow you to instantiate a task with resource name as the only input argument. In order to achieve that, I will create a sensor that as the input argument can take a resource name:

{% highlight javascript linenos %}
var meterNumber = options.requiredProperties.meterNumber || waylayUtil.getResource(options)
{% endhighlight %}

In this example, the sensor that will fetch the data from the external system needs only one input argument _meterNumber_. That input argument can be either defined on the sensor level (via properties), or like in this case, via the resource concept. 
When you associate the sensor with a resource name, you need specify a resource on the node level as: 

* fixed resource name (e.g. house1, deviceX, meter123...)
* $ (resource name will be inherited from the task resource name)
* $taskId (resource name will be inherited from the task ID)

_getResource_ call will automatically translate $, $taskId into the runtime resource name. Waylay engine will make sure that correct translation happened. There is another use case where you can use resource concept (stream data), but more about that in the future posts.

Once you have created a template you can start it using a REST call [rest link] [rest]. In this example, we call the template _dailyConsumption_ with resource defined as an integer. Once the task is started, this resource name (in this case a number) will be propagated to the correct sensor. 

{% highlight javascript linenos %}
for i in {1..100000}
do
   echo "start task for meter $i"
   curl --user KEY:PWD -H "Content-Type:application/json" -X POST -d '{
   "name": "consumption for meter '$i'\n",
   "template": "dailyConsumption",
   "resource": "'$i'",
   "start": true,
   "resetObservations": true,
   "type": "periodic",
   "frequency": 86400000
 }' https://app.api/tasks
done

{% endhighlight %}
[doc]: http://docs.waylay.io/Tasks-and-Templates.html
[rest]: http://docs.waylay.io/Waylay-REST-API-documentation.html
