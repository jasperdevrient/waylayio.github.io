---
layout:     post
title:      "Sigfox support"
date:       2016-02-27 12:51
categories: sigfox
---

It's now possible to connect a Sigfox Device type to the Waylay dashboard. This allows for the dashboard to receive all the messages a Sigfox device type sends.

# Connecting to a Device type
To connect to Sigfox begin by making sure that you are logged in in both the Sigfox and the Waylay dashboard.

1. Get the API login and API password from the Sigfox backend by going to 'group' then select your preferred group and go to the tab API Access. If needed create an API access entry and give it all the roles. Then copy the credentials to the (Waylay) Dashboard Sigfox authentication form.
2. To get a device type id, go to the 'DEVICE TYPE' page and choose the device type that you prefer (click link in the column Name). Now you see information about this device type. Use the field Id in the field 'DeviceType ID' in the Dashboard Sigfox authentication form.
3. Specify how the messages for this device type is formatted. The format is the same as the one Sigfox uses and will be explained later.
4. When all fields are configured, click the authenticate button. This will import old data and adds a webhook that refers to the Waylay dashboard to the selected device type. Should it not exist you can press the Refresh resources button under DEBUG or create the webhook yourself.
5. Now when a Sigfox message is received it will be propagated to Waylay. The message send to the broker depends on the format string you have provided.

# Sigfox message type decoding grammar
The Sigfox message type decoding grammar consists of one or more fields separated by a space. A fields has a few properties separated by the `:` character:

- The field name is an identifier including letters, digits and the '-' and '_' characters.
- The byte index is the offset in the message buffer where the field is to be read from, starting at zero. If omitted, the position used is the current byte for boolean fields and the next byte for all other types. For the first field, an omitted position means zero (or the start of the message buffer).
- Next comes the type name and it's parameters, which varies depending on the type:
 - **boolean**: parameter is the bit position in the target byte.
 - **char**: parameter is the number of bytes to gather in a string.
 - **float**: parameters are the length in bits of the value, which can be either 32 or 64 bits, and optionally the endianness for multi-bytes floats. Defaults to big endian.
 - **uint** (unsigned integer): parameters are the number of bits to include in the value and optionally the endianness for multi-bytes integers. Defaults to big endian.
 - **int** (signed integer): parameters are the number of bits to include in the value and optionally the endianness for multi-bytes integers. Default is big endian.

 An example of the a format string would be `int1::uint:8 int2::uint:8` which would for the data *1234* result in the output `{ int1: 0x12, int2: 0x34 }` as a JSON object.

# Creating your own webhook
The parsing of the messages happens in the webhook. When a message is received, the webhook will use the *@waylay/sigfox-parser* package and parse the message according to the previously specified format string. The webhook wil receive a message in this format:

{% highlight javascript linenos %}
{
  "longitude": "4",
  "latitude": "51",
  "device": "151D8",
  "data": "144418",
  "timestamp": "1461321009",
  "payloadConfig": "lightAmbi::uint:16 temperature:2:int:8"
}
{% endhighlight %}

And then parses the data value this.

{% highlight javascript linenos %}
{
  "lightAmbi": 5188,
  "temperature": 24
}
{% endhighlight %}

And then you should combine these messages and send this to all the resources where the deviceid is equal to the device. NOte that sometimes the parsed message already contains geodata and this should not be overwritten. Below is the code that implements the webhook (requires are left out).

{% highlight javascript linenos %}
module.exports =  function(req, res) {
  res.sendStatus(200);
  try {
    const body = req.body;
    const message = parseMessage(body.data, body.payloadConfig);
    message.timestamp = body.timestamp * 1000;
    if (!message.longitude && !message.latitude) { // Make sure the parsedMessage does not contain geolocation
      message.longitude = body.longitude;
      message.latitude = body.latitude;
    }
    Resource.find({provider: 'sigfox', providerId: body.device})
      .then((devices) => {
        return devices.map((device) => waylay.data.postSeries(device.resource, message));
      });
  } catch (err) {
    log.error('ERROR', err);
  }
};
{% endhighlight %}
