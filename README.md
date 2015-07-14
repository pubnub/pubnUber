# Clone Uber with PubNub and EON Maps

Today we're going to use Phonegap and PubNub to create a simple taxi hailing app. What we'll walk through today is the same technology stack used by Uber, Gett, and other taxi hailing apps. They all work in a similar fashion.

## Getting started

1. Install [phonegap](https://www.npmjs.com/package/phonegap) on the CLI.
2. Clone this repository locally.
3. The repo contains two directories ```/rider``` and ```/driver```. We're going to start with ```/driver```.


## Driver

Everything is running from ```driver/index.html```.

We first need to include EON in the ```<head>``` of the document.

```html
<script type="text/javascript" src="http://pubnub.github.io/eon/lib/eon.js"></script>
<link type="text/css" rel="stylesheet" href="http://pubnub.github.io/eon/lib/eon.css" />
```

EON is a realtime IOT framework that will handle our map and facilitate the communication between our phones,

### Creating the map

First we're going to create a ```<div>``` with the id ```map``` so we can locate passengers. This container will hold our map object. In the CSS above it's set to 100% width and 100% height.

```css
#map {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
```

```html
<div id='map'></div>
```

Now that we have our map placeholder, we'll use EON to create a realtime map of passengers.

The following javascript will set up our map.

```js
var map = eon.map({
    id: 'map',
    mb_id: 'ianjennings.lec77m70',
    mb_token: 'pk.eyJ1IjoiaWFuamVubmluZ3MiLCJhIjoiZExwb0p5WSJ9.XLi48h-NOyJOCJuu1-h-Jg',
    channel: 'rutgers-bus-data',
    history: true,
    pubnub: pubnub,
    marker: function (latlng, data) {
        var marker = new L.Marker(latlng, { 
            icon: L.icon({
              iconUrl: 'http://i.imgur.com/VEVcNdY.png',
              iconSize: [15, 30]
            })
        });
        return marker;
    }
});
```

There's a lot going on in this config, here's a quick chart explaining the parameters. 

| Parameter | Value | Explanation   |
|-----------|-------|---------------|
| id | map | Id of the DOM element in which the map will be rendered. |
| mb_id | ianjennings.lec77m70 | Mapbox ID |
| mb_token | pk.eyJ1IjoiaWFuamVubmluZ3MiLCJhIjoiZExwb0p5WSJ9.XLi48h-NOyJOCJuu1-h-Jg | Mapbox Token |
| channel | rutgers-bus-data | PubNub channel to monitor drivers | 
| history | true | Restore old PubNub data | 
| pubnub | pubnub | Serve the same pubnub object we use above into EON |
| marker | function (latlng, data) {} | A function that allows us to create dynamic markers |

The marker function, return a person icon.

```js
function (latlng, data) {
    var marker = new L.Marker(latlng, { 
        icon: L.icon({
                iconUrl: 'http://i.imgur.com/VEVcNdY.png',
                iconSize: [15, 30]
            })
        });
    return marker;
}
```

Define our PubNub channel.

```js
var channel = "pubnub-taxi-app";

var pubnub = PUBNUB.init({
    publish_key: 'demo',
    subscribe_key: 'demo'
});
```

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

Run this in browser. Now we have a map of people (buses) rendered on a map.

![](http://www.pubnub.com/developers/connected-car/images/bae45509.location-tracking.png)

![](http://i.imgur.com/AADA3Sg.gif)

