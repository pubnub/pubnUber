# Clone Uber with PubNub and EON C3

Today we're going to use Phonegap and PubNub to create a simple taxi hailing app.

Install [phonegap](https://www.npmjs.com/package/phonegap) on the CLI.

Clone this repo.

Rider and Driver.

Start with Driver.

Everything is running from ```driver/index.html```.

```css
#map {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
```

Include EON.

```html
<script type="text/javascript" src="http://pubnub.github.io/eon/lib/eon.js"></script>
<link type="text/css" rel="stylesheet" href="http://pubnub.github.io/eon/lib/eon.css" />
```

EON is a realtime IOT framework that includes PubNub and will facilitate the communication between our phones.

Define a ```div``` that will hold our map object. In the CSS above it's set to 100% width and 100% height.

```html
<div id='map'></div>
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

Set up EON framework. 

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

Run this in browser. Now we have a map of people (buses) rendered on a map.

![](http://www.pubnub.com/developers/connected-car/images/bae45509.location-tracking.png)

![](http://i.imgur.com/AADA3Sg.gif)

