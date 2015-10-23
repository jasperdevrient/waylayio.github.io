---
layout:     post
title:      "waylay Integration with database using native connector"
date:       2015-10-23 17:19:06
categories: integration
---
Hi everybody, in this post we will show how to turn any database into a waylay sensor.

#Turning database into a sensor

Waylay comes with sanboxed nodejs VM in which scripts get executed, similar to AWS lambda. Sanboxed VM's come with number of packages that are pre-installed. 
You can simply access them from the script, no need to declare them. Feel free to check the Waylay [documentation][waylaydocs] in order to see complete list.
In this blog we will use native Microsoft SQL connector.

#Integration

In waylay we don't talk about connectors, but rather about sensors and actuators. In that respect, you can think of the database record as a sensor: e.g. 
_Customer_, or an actuator: e.g. database log entry (_Incident_ etc). More about the phylosophy behind our concepts you can find here [blogpost][blog].
In this example, I will search for a device in the database and send back the record item as the raw data - if ther record is found. I will also
use states "Found"/"Not Found" to tell the user of this sensor whether ther record has been found in the database. This can be handy in case 
you use the conditional execution of the sensors feature, where the outcome of one sensor can trigger execution of the next sensor. For instance, you may want 
to implement a use case where the smart meter collection triggers search in the ERP database, after which an e-mail needs to be send to the user of this meter 
(while the e-mail address is in the ERP database). In this example _"Found"_ state of the ERP sensors would trigger e-mail actuator, and the e-mail address itself 
would come from the raw data of the same sensor. So, let's see the code:

{% highlight javascript linenos %}

/*
 find a customer by meter ID. Since we assume that we first get a meter data and only then search for a customer, assumption is that the
 customer ID needs to be lookup based on the meter sensor. Therefore we are looking at the meterID from the rawData. If the use case was other way arround 
 (first we fetch customer), then we would need first to get a customer and use meterId to find a meter measurements.
*/
var server = options.globalSettings.DB_HOST;
var user = options.globalSettings.DB_USER;
var password = options.globalSettings.DB_PASSWORD;
var table = options.globalSettings.DB_TABLE;
var database = options.globalSettings.DB_DATABASE;


var meterNumber;
try {
    //this call always throughs the exception
    console.log("try to fetch meter from the raw data");
    meterNumber = waylayUtil.getRawData(options, options.requiredProperties.meterNumber);
} catch(err){
    console.log("try to fetch meter from the input argument");
    meterNumber = options.requiredProperties.meterNumber || options.node.RESOURCE || options.task.RESOURCE; 
}

if(meterNumber === undefined ){
    send(new Error("meter number not found"));
} else {
        var config = {
        user: user,
        password: password,
        server: server,
        database: database
    }
    try{
        mssql.connect(config, function(err) {
            if(err) {
                console.log("error, enable to connect")
                console.log(err);
                 send(new Error(err));
            } else {
                var request = new mssql.Request();
                console.log("request...");
                request.query('select * from ContactInfo where MeterNumber='+meterNumber, function(err, recordset) {
                if(err) {
                    console.log("error...")
                    console.log(err);
                    send(new Error("error"));
                } else{
                        var res = {
                            observedState : "Not Found"
                        };
                        console.log(recordset);
                        if(recordset.length == 1) {
                            res.observedState = "Found";
                            res.rawData = recordset[0];
                            send(null, res);
                        } else
                            send(null, res);
                    }
                });
            }
        });
    }
    catch(err){
        console.log("Exception connection error:")
        console.log(err);
        send(new Error(err));
    }
}

{% endhighlight %}

[waylaydocs]:     http://docs.waylay.io/Plugin-API.html
[blog]: http://www.waylay.io/waylay-engine-rules-engine-rule/


