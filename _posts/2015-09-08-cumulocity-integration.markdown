---
layout:     post
title:      "Rule your devices with waylay and Cumulocity"
date:       2015-09-08 17:19:06
categories: integration
---
Hi, this post including waylay platfrom integration with Cumulocity platform.

#Cumulocity

Cumulocity is a cloud based development platform, you can access more information in [here](http://cumulocity.com/about/).

#Integration

This blog post will show you how you can connect a device and data of it from Cumulocity to waylay.


#What you need

* A waylay subscription
* A Cumulocity subscription
* An abstract or real devices to use in Cumulocity platform.

#Entering Cumulocity Platform

Now open your browser and go to below link
'https://yourUserName.cumulocity.com/apps/administration/index.html#/'
Enter the username that you used when creating Cumulocity account instead of ‘yourUserName’ on link.

You will reach Cumulocity dashboard

![Cumulocity Dashboard]({{ site.baseurl }}/assets/images/cumulocity_dashboard.png)

This is how the connections are set up

Now we have to create a device.

If you have one of these devices on the list [Certificated Devices](https://www.cumulocity.com/dev-center/) you can add them too .

![Cumulocity Dashboard]({{ site.baseurl }}/assets/images/cumulocity_dashboard2.png)

Choose Devicemanagament from list on the top right.

#Choosing Device

![Cumulocity Dashboard]({{ site.baseurl }}/assets/images/cumulocity_device.png)
On the Devicemanagament page choose ‘All devices’ there will be a device named ‘A Demo Device’ click that and customize device according to your pleasure and click ‘Save the changes’ at bottom.

Now we have a device and we will use waylay to get signal measurements of this devices.
 You can learn more about [Measurement API](http://www.cumulocity.com/guides/reference/measurements/) by clicking.

Then connect your waylay account and click 'Your User Name' --> Profile

![Cumulocity Dashboard]({{ site.baseurl }}/assets/images/cumulocity_waylay.png)

Choose ‘Global settings’ on left panel.

And add a property named ‘Cumulocity_Key’ with your Cumulocity key.

Now choose ‘Create’ ‘+ Sensor’ from top menu.

Replace all code with bottom one, don't forget to change API link with your own.

{% highlight javascript linenos %}
var token = options.globalSettings.Cumulocity_Key;
var dateTo = options.requiredProperties.dateTo || moment().toISOString();
var yesterday = new Date();
yesterday.setDate(yesterday.getDate() - 1);
var dateFrom = options.requiredProperties.dateFrom || moment(yesterday).toISOString();
var source = options.requiredProperties.source;

if(token !== undefined && source !== undefined)
{

     var options = {
        url: 'http://yourUserName.cumulocity.com/measurement/measurements/series?dateTo='+dateTo+'&source='+source+'&dateFrom='+dateFrom,

        headers: {
            Authorization: "Basic "+ token,
            Accept: "application/json"
        }
    };

    var callback = function(error, response, body) {
        if (!error && response.statusCode == 200) {
            var bodyJson = JSON.parse(body);

            var value = {
                rawData:bodyJson,
                observedState : Object.keys(bodyJson["values"]).length > 0 ? "Found" : "Not Found"
            };

            send(null,value);
        }else{
            send(new Error("Calling api.cumulocity failed"));
        }
    }

    request.get(options, callback);
}else{
    send(new Error("Missing token or source"));
}

{% endhighlight %}

Change ‘States’ , ‘Properties’ and ‘Raw data’ according to bottom picture.

![Cumulocity Dashboard]({{ site.baseurl }}/assets/images/cumulocity_options.png)

If you done both of steps click ‘Update’ on the top and get designer screen from main menu of waylay.

Now select sensor that you create and drop it on designer and add an actuator.

I choose mail actuator it is looking like this.

![Cumulocity Dashboard]({{ site.baseurl }}/assets/images/cumulocity_sensor.png)

Now we have to modify properties.

![Cumulocity Dashboard]({{ site.baseurl }}/assets/images/cumulocity_properties.png)

In Cumulocity time/date format is [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)
Let’s enter properties.
dateTo: 2015-09-08T01:55:28Z
dateFrom : 2014-09-08T01:55:28Z
source: 10200

Source parameter is ID of device that you can find it when you click the device it is on the bottom.

![Cumulocity Dashboard]({{ site.baseurl }}/assets/images/cumulocity_deviceID.png)

After you finish entering mail properties you are ready to learn your device’s signal mesuarement’s via mail over waylay platform !
