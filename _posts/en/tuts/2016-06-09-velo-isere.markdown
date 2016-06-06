---
layout: tut
title: "Vélo Isère"
date: 2016-06-09 12:00:00
author: abenrob
published: true
posttype: tutorial
lang: en
categories:
- en
---
In this tutorial, we are going to explore the components of route-finding APIs, and build a simple application to find a bike route between two points. We are going to use html5 geolocation to pin-point our location, the [BAN](https://adresse.data.gouv.fr/api/) database of french addresses to find our destination, and [itinIsère](http://www.itinisere.fr/fr/api-open-services/169/OpenData/openservices) and [métromobilité](http://www.metromobilite.fr/pages/opendata/OpenDataApi.html) to find the actual routes.
<!--more-->

## House keeping

To successfully follow this tutorial you need a text editor (such as Notepad++ or Atom). Go ahead and use your personal favorite, or if you don't have one installed,
download [Atom Editor](https://atom.io/).

You will also need to be able to run a local webserver in order to access remote resources, such as APIs. The easiest way to do this if you have python installed is to navigate in the console to the root of the project, and run ```python -m SimpleHTTPServer```. This will enable a webpage at [http://localhost:8000](http://localhost:8000).

# Getting Started

## Websites
Since we will be building a simple web application in the workshop, we should touch on some of the components of a website first.

A basic website is comprised of HTML, css, and javascript files. The HTML (Hyper Text Markup Language) is the structure of the page - it indicates what goes where, as well as referencing the other components of the page. CSS (Cascading Style Sheets) are the style rules we put in place to dictate how we want the HTML elements to appear. Javascript is a programming language used by websites to *do* things - get data, manage interactions, format data, etc. [jQuery](https://jquery.com/) is a javascript library that can make document manipulation, event handling and data fetching much simpler. [Bootstrap](http://getbootstrap.com/) is a framework that includes css and javascript helpers to make styling a website much easier as well (without developing the styles ourselves.) This can be great for prototyping sites, or making a decent looking tutorial in less time ;).

## The basic webmap
We are also going to use [MapboxJS](https://www.mapbox.com/mapbox.js/api/v2.4.0/) (which extends [LeafletJS](http://leafletjs.com/)) to get the map part of the application in place in very few lines of code.

In our project root, we will create a file called 'index.html', a file called 'style.css' and a file called 'script.js'.

In index.html we put this content:

~~~markup
<!DOCTYPE html>
<html>

<head>
  <meta charset=utf-8 />
  <title>VéloIsère</title>
  <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
  <link href='https://api.mapbox.com/mapbox.js/v2.4.0/mapbox.css' rel='stylesheet' />
  <link href='style.css' rel='stylesheet' />
  <script src='https://api.mapbox.com/mapbox.js/v2.4.0/mapbox.js'></script>
</head>

<body>
  <div id="map"></div>
  <script src="script.js"></script>
</body>

</html>
~~~

You will notice in the <head> section that we have included references to our style.css, as well as the mapbox.js. Below the map container (<div id="map"></div>) we put our script.js reference, so the mapping library isn't called before the map container is rendered.

In style.css, put this content:

~~~css
body {
  margin: 0;
  padding: 0;
}
#map {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
}
~~~

Here we tell the page and map container to be full width and height.

And in script.js, put this content:

~~~javascript
// define mapbox token for map creation
L.mapbox.accessToken = 'pk.eyJ1IjoiYWJlbnJvYiIsImEiOiJEYmh3WWNJIn0.fus8CLBKPBHDvSxiayhJyg';

// set up map
var map = L.mapbox.map('map', 'mapbox.streets')
    .setView([45.186502, 5.736339], 13);
~~~

Here we tell the mapbox library to use our token (mapbox is a subscription service, with a free tier) and to create a map using the 'streets' style, centered on Grenoble, and assigned it to the HTML element with the id of 'map'.

Fire up your webserver, and check out the page we created:

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="YWXdPN" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>
<!-- end codepen embed -->

# Geolocation
Geolocation allows us to get the user's location (in geographic coordinates) and is part of the HTML5 spec. Since we need a starting point for our route, we are going to play with geolocation in the process.

The geolocation object is a part of the browser's navigator object. Most modern browsers support geolocation, but Chrome 50+ (and probably all the others as well) no longer support it from unsecure origins. That means a public website without SSL (HTTP not HTTPS) can not use geolocation in chrome 50+. Luckily for us, Firefox still supports it, and localhost is a secure origin, so the demo should work locally, even in chrome.

Add to script.js:

~~~javascript
// define global variables
var pos = [];
var posMarker;
var addressPos = [];
var addressMarker;
var bikeRouteMM;
var bikeRouteII;

// function for successful geolocation
function geoSuccess(position) {
  pos = [position.coords.latitude, position.coords.longitude];

  // tell map to go to the new position
  map.panTo(pos);

  // remove the marker if it is already on the map
  if (posMarker) {map.removeLayer(posMarker);};

  // create a new mapbox marker for our position, add it to the map
  posMarker = L.marker(pos, {
    icon: L.mapbox.marker.icon({
      'marker-size': 'large',
      'marker-symbol': 'bicycle',
      'marker-color': '#fa0',
    }),
  }).addTo(map);
}

// function for geolocation error
function geoError(err) {
  // tell user of issue
  alert('Sorry, no position available.');
}

// use html5 geolocation
function getUserLocation() {
  if ('geolocation' in navigator) {
    navigator.geolocation.getCurrentPosition(geoSuccess, geoError);
  } else {
    console.log('No geolocation available');
  }
}

// call the actual function now
getUserLocation();
~~~

Something to notice is the use of callback functions (geoSuccess and geoError in this case). Functions in javascript can be either *synchronous* or *asynchronous*. *Synchronous* functions finish running before allowing the script to move on to the next step. That is fine for many things, as the time to run many functions is unnoticeable. For longer functions (especially those that need to *go get data*), we don't want the application to finish getting all the data before moving on. We want to tell the app to get the data, and tell it what to do when the data are successfully received. This patten is called *asynchronous*.

And the results again (try firefox, chrome 50+ will no longer function...)

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="pbJYZK" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<!-- end codepen embed -->

# APIs
An API (Application Programming Interface) is a set of instructions that can be used to tell an application what to do. Recently, API is frequently used to describe a set of routes that can be accessed to get, update or put data from remote sources. In our case, we are only interested in getting data (Addresses and Routes) and in [JSON](https://en.wikipedia.org/wiki/JSON) (javascript object notation) format, which is an human-readable data structure which is easy to use and parse in javascript applications.

To access these remote data sources, we are going to use [jQuery](https://jquery.com/), a javascript helper library which is useful for document traversal, user event handling, and data access via [Ajax](https://en.wikipedia.org/wiki/Ajax_(programming)) (asynchronous javascript and xml, although we use it for JSON as well). jQuery has a json-specific helper-class called [getJSON](http://api.jquery.com/jquery.getjson/) which we will use to get our data. A basic getJSON pattern is written like this:

~~~javascript
$.getJSON( 'url/to/my/data/', function( json ) {
  // this part is the asynchronous callback
  console.log('Look, I got this new data: ' + json.dataname);
 });
~~~

# Addresses
In order to create a route, we need two points: start and end. We have the start (our geolocation), and for the end, we are going to use [BAN](https://adresse.data.gouv.fr/) (la Base Addresse National Française).

We are going to use the getJSON function, and log the results to the console sp we can explore them. (Feel free to delete the "getUserLocation();" line in script.js - we'll use it later.)

Add jQuery to index.html, right after the style.css reference

~~~markdown
<script src='https://cdnjs.cloudflare.com/ajax/libs/jquery/2.0.0/jquery.min.js'></script>
~~~

Relead your page, then open the console [see this SO question for help](http://webmasters.stackexchange.com/questions/8525/how-to-open-the-javascript-console-in-different-browsers).

Paste this in the console, and hit enter.

~~~javascript
$.getJSON('https://api-adresse.data.gouv.fr/search/?q=31 rue gustave eiffel', function (data) {
  console.log(data);
});
~~~

We can see the results here (each "feature" is a found address):

<!-- json embed -->
<div id="json1" class="well"></div>
<script>$(window).bind("load", function() {
  $.getJSON('https://api-adresse.data.gouv.fr/search/?q=31 rue gustave eiffel', function (data) {
    document.getElementById("json1").innerHTML = JSONTree.create(data);
  });
});</script>
<!-- end json embed -->

The BAN API allows us to provide locational precision to our results, so we are returned the nearest matches first. Try the same console code from before (press the up arrow when in the console to access previous commands), but add latitude,longitude to the end of the request url like so: ```&lat=45.186502&lon=5.736339```. Notice how the Grenoble result moves to first place?

Let's build a function to put a point on the map for a found address.

Add to script.js:

~~~javascript
// get address from BAN database https://adresse.data.gouv.fr/api/
function getBanAddressCoords(q, lat, lon, callback) {
  // build uri for ban data
  var uri = 'https://api-adresse.data.gouv.fr/search/?q=' + q;
  if (lat && lon) { uri = uri + '&lat=' + lat + '&lon=' + lon; };

  $.getJSON(uri, function (data) {
    // grab the first address (feature);
    var coords = data.features[0].geometry.coordinates;

    // remove marker from map if it exists
    if (addressMarker) {map.removeLayer(addressMarker);};

    addressPos = [coords[1], coords[0]];

    // create a new mapbox marker for our position, add it to the map
    addressMarker = L.marker(addressPos, {
      icon: L.mapbox.marker.icon({
        'marker-size': 'large',
        'marker-symbol': 'rocket',
        'marker-color': '#66ccff',
      }),
    }).addTo(map);

    if (pos.length > 0) {
      // we have a position, use it to zoom to both points
      var group = new L.featureGroup([posMarker, addressMarker]);
      map.fitBounds(group.getBounds().pad(0.5));
    } else {
      // no position, just pan to new point
      map.panTo(addressPos);
    }

    callback();
  });
};

getBanAddressCoords('31 rue gustave eiffel', 45.186502, 5.736339, function () {
  console.log('point added');
});
~~~

And the results again:

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="EyVjbb" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<!-- end codepen embed -->

# Routes
Now that we know how to get our start and end points, we can explore the routing APIs.

## Métromobilité
The [Métromobilité API](http://www.metromobilite.fr/pages/opendata/OpenDataApi.html) has a resource called [Horaires OTP](http://www.metromobilite.fr/pages/opendata/OpenDataApi.html#Otp), which uses [OpenTripPlanner](http://www.opentripplanner.org/) to provide multimodal route-finding based on their data. To get a bike route, we need only provide start coordinates, end coordinates and mode, which in our case is 'BICYCLE'.

Let's try it out. Paste this in the console, and hit enter.

~~~javascript
$.getJSON('http://data.metromobilite.fr/otp/routers/default/plan?mode=BICYCLE&fromPlace=45.1836656,5.703573&toPlace=45.195926,5.735935', function (data) {
  console.log(data);
});
~~~

We can see the results here (the routes are in plan.itineraries):

<!-- json embed -->
<div id="json2" class="well"></div>
<script>$(window).bind("load", function() {
  $.getJSON('http://data.metromobilite.fr/otp/routers/default/plan?mode=BICYCLE&fromPlace=45.1836656,5.703573&toPlace=45.195926,5.735935', function (data) {
    document.getElementById("json2").innerHTML = JSONTree.create(data);
  });
});</script>
<!-- end json embed -->

For the mapping exercise, we want the geometry of the trip. In plan.itineraries[0].legs[0].legGeometry.points, we see an encoded string "{|wrGe}xa@_CCK??M?C?u@@I?k@@y@@e@?]DmD@S@Q\sFVyEFmA@KEIkAwBIOGKISgA{BACGOm@sBGQEOe@aBIUc@}AGc@AKAO_@sDA_@Ag@AM?W?e@?c@?Q?M?i@?WAcB?M?uA?QAy@AoB?S@w@@Q?W?S?O@_@?OBkB?O?Q@e@@QBYBk@@QHmA@WLgB@]QKu@g@a@UkAq@sAy@_DkBECIEOQ{@y@}B}B?O?QEYEe@@_AA_@ESGQCEs@yAm@wAGGCCEGGQACAGAGM[M[ISEMCKEa@Iq@Ku@O{@Wm@Qc@IQG[AQBKOCEAMCuAmAeAgAm@g@eA{@UOBm@@y@?QCo@Ec@Ik@Ug@[_@@e@@YIu@MUMUOUMOIKi@e@c@_@HQGIKQIQMKy@q@YU??OMYU_@_@[a@_@g@sCcEQU[c@c@o@CCIMSWQe@Ca@CKC_C@W"

## IninIsère

# App Build

# Resources

* The learning curve can be pretty steep. Stay positive
* Start simple, add complexity piece by piece
* Refer to documentation/tutorials
* **Cannibalize code** wherever/whenever you can. Mapbox has great examples and most the code is freely accessible. See something you like? Take a look at how it's done!
