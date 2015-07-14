# Clone Uber with PubNub and EON Maps

Today we're going to use Phonegap and PubNub to create a simple taxi hailing app. What we'll walk through today is the same technology stack used by Uber, Gett, and other taxi hailing apps. They all work in a similar fashion.

## Getting started

1. Install [phonegap](https://www.npmjs.com/package/phonegap) on the CLI.
2. Clone this repository locally.
3. The repo contains two directories ```/rider``` and ```/driver```. We're going to start with ```/driver```.



### Set Up PubNub

First, we need to set up PubNub. the ```PUBNUB``` variables comes for free from the EON library. The ```publish_key``` and ```subscribe_key``` settings come from your [PubNub account](http://pubnub.com).

```js
var channel = "pubnub-taxi-app";

var pubnub = PUBNUB.init({
    publish_key: 'demo',
    subscribe_key: 'demo'
});
```


The ```pubnub``` plugs the same ```pubnub``` variable we configured in the first step into EON. This lets us use our own pubnubu keys within EON.


Our Mapbox configuration is set and we're ready to send some 

## Publish messages to the channel

Define our PubNub channel.

Subscribe.

```js
pubnub.subscribe({
    channel: channel,
    message: function(message,env,channel){

        if(message == 'hail') {

            var pickup = confirm("Someone requested a ride!", "Accept");

            if(pickup) {

                pubnub.publish({
                    channel: channel,
                    message: 'pickup'
                });

            }

        }
    }
});
```

And here is the ```hail()``` function.

```js
var hail = function(argument) {

    pubnub.publish({
        channel: channel,
        message: 'hail'
    });

    alert('Waiting for driver...');

};
```

![](http://www.pubnub.com/developers/connected-car/images/bae45509.location-tracking.png)

![](http://i.imgur.com/AADA3Sg.gif)