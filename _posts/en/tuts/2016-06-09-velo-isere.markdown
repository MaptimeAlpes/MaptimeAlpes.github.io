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
<div id="getJson1" class="btn btn-sm btn-primary">get JSON</div>
<div id="json1" class="well json-block"></div>
<script>$('#getJson1').click(function() {
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
var results;
$.getJSON('http://data.metromobilite.fr/otp/routers/default/plan?mode=BICYCLE&fromPlace=45.1836656,5.703573&toPlace=45.195926,5.735935', function (data) {
  console.log(data);results = data;
});
~~~

We can see the results here (the routes are in plan.itineraries):

<!-- json embed -->
<div id="getJson2" class="btn btn-sm btn-primary">get JSON</div>
<div id="json2" class="well json-block"></div>
<script>$('#getJson2').click(function() {
  $.getJSON('http://data.metromobilite.fr/otp/routers/default/plan?mode=BICYCLE&fromPlace=45.1836656,5.703573&toPlace=45.195926,5.735935', function (data) {
    document.getElementById("json2").innerHTML = JSONTree.create(data);
  });
});</script>
<!-- end json embed -->

For the mapping exercise, we want the geometry of the trip. Since we assigned the returned data to the results variable, we can play with the data in the console. To see the route geometry, type ```results.plan.itineraries[0].legs[0].legGeometry.points``` in the console. We see an encoded string, which contains the geometry of the entire route. Also check out ```results.plan.itineraries[0].legs[0].steps```. This provides coordinates and directions. Think how we could use this to message each part of the route to the user.

Hold on, that geometry is nonsense! To make the route geometry useful, we'll need to decode it. Mapbox created a helper library to do just that, called [polyline](https://github.com/mapbox/polyline).

Add polyline to index.html, right after the jQuery reference

~~~markdown
<script src='https://rawgithub.com/mapbox/polyline/master/src/polyline.js'></script>
~~~

In script.js, you can remove the call to the address function for now. Add these functions to the bottom of the file now (Function to clear any routes from the map, function to create métromobilité route, call to route creator):

~~~javascript
// function to clear routes from map
function clearRoutes() {
  if (bikeRouteMM) {map.removeLayer(bikeRouteMM);};

  if (bikeRouteII) {map.removeLayer(bikeRouteII);};
};

function getMetromobiliteRoute(fromPos, toPos, callback) {
  clearRoutes();

  // build uri for API
  var uri = 'http://data.metromobilite.fr/otp/routers/default/plan' +
            '?mode=BICYCLE&fromPlace=' + fromPos + '&toPlace=' + toPos;

  $.getJSON(uri, function (data) {
    // get all node points from first returned trip
    var pts = data.plan.itineraries[0].legs[0].legGeometry.points;

    //use mapbox.polyline to decode encoded polyline string
    var decoded = polyline.decode(pts);

    // create polyline, assign color, bind popup, add to map
    bikeRouteMM = L.polyline(decoded, { color: '#21881c' }).bindPopup('Metromobilité').addTo(map);

    callback();
  });
};

getMetromobiliteRoute([45.1836656,5.703573], [45.195926,5.735935], function(){
  console.log('route completed')
});
~~~

And the results again:

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="gMamNd" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<!-- end codepen embed -->

## ItinIsère
The [ItinIsère API](http://www.itinisere.fr/fr/api-open-services/169/OpenData/openservicesml) has a resource called [JourneyPlanner/v2/BikeTrip/json](http://www.itinisere.fr/fr/api-open-services/169/OpenData/openservices#!/journeyplanner/v2/BikeTripJsonGET_get_1), which we can use to get bike routes based on their data. To get a bike route, we need to provide start coordinates, end coordinates, start date, start time and algorithm. In our case, we will use the 'FASTEST' algorithm. ItinIsère requires an API key to use their API, which you can request in your profile. For this exercise, we can just use Maptime-Alpes' key, but you should request your own for future development.

Let's try it out. Paste this in the console, and hit enter.

~~~javascript
var results;
$.getJSON('http://www.itinisere.fr/webServices/TransinfoService/api/journeyplanner/v2/BikeTrip/json?DepLat=45.1837081&DepLon=5.7035291&ArrLat=45.195926&ArrLon=5.735935&Date=2016-06-07&DepartureTime=10-48&user_key=0016bf2ff47f630bab2e65bba954c091&Algorithm=FASTEST&callback&callback=?', function (data) {
  console.log(data);results = data;
});
~~~

We can see the results here (the route segments are in trips.Trip[0].sections.Section[0].Leg.pathLinks.PathLink):

<!-- json embed -->
<div id="getJson3" class="btn btn-sm btn-primary">get JSON</div>
<div id="json3" class="well json-block"></div>
<script>$('#getJson3').click(function() {
  $.getJSON('http://www.itinisere.fr/webServices/TransinfoService/api/journeyplanner/v2/BikeTrip/json?DepLat=45.1837081&DepLon=5.7035291&ArrLat=45.195926&ArrLon=5.735935&Date=2016-06-07&DepartureTime=10-48&user_key=0016bf2ff47f630bab2e65bba954c091&Algorithm=FASTEST&callback&callback=?', function (data) {
    document.getElementById("json3").innerHTML = JSONTree.create(data);
  });
});</script>
<!-- end json embed -->

Again, for the mapping exercise, we want the geometry of the trip. To see the route geometry, type ```results.trips.Trip[0].sections.Section[0].Leg.pathLinks.PathLink[0]``` in the console. We see a "LINESTRING ()" string in the Geometry field, which contains the geometry of that specific segment of the route. We also have the directions for the segment, which could be used to communicate to the user.

The Geometry are presented in [WKT (Well-Known text)](https://en.wikipedia.org/wiki/Well-known_text) format. To use in our map, we need to convert the WKT to a format the map can read. Mapbox (again) has created a helper library ([wellknown](https://github.com/mapbox/wellknown)) to convert WKT to geoJSON, which we can then use or parse.

The ItinIsère data will require a little bit more work to display on the map: we need to convert each segment's WKT to geoJSON, but that would leave us with a feature for each segment, whereas we want a single line. Using javascript's *map* and *reduce* functions, we can convert the wkt with *map*, combine all the segments to one line with *reduce* and then swap [longitude,latitude] to [latitude.longitude] with another *map*.

First off, let's include the wellknown library, by putting the reference under the polyline reference in index.html.

~~~markdown
<script src='https://rawgithub.com/mapbox/wellknown/master/wellknown.js'></script>
~~~

In script.js (you can remove the call to getMetromobiliteRoute() if you'd like) we will put this at the end of the file:

~~~javascript
function getItinisereRoute(fromPos, toPos, algorithm, callback) {
  clearRoutes();

  // get date elements to fill in date/time requirement of API
  var d = new Date();
  var dateString = d.toISOString().slice(0, 10);
  var timeString = d.getHours() + '-' + (d.getMinutes() < 10 ? '0' : '') + d.getMinutes();

  // build uri for API
  var uri = 'http://www.itinisere.fr/webServices/TransinfoService/api/journeyplanner/v2/' +
            'BikeTrip/json?DepLat=' + fromPos[0] + '&DepLon=' + fromPos[1] +
            '&ArrLat=' + toPos[0] + '&ArrLon=' + toPos[1] +
            '&Date=' + dateString + '&DepartureTime=' + timeString +
            '&user_key=0016bf2ff47f630bab2e65bba954c091&Algorithm=' + algorithm + '&callback=?';

  $.getJSON(uri, function (data) {
    // get links for first trip
    var pathLinks = data.trips.Trip[0].sections.Section[0].Leg.pathLinks.PathLink;

    // isolate geometry from WKT string
    var linkCoords = pathLinks.map(function (link) {
      return wellknown.parse(link.Geometry).coordinates;
    });

    // combine all legs into one line
    var coords = linkCoords.reduce(function (a, b) {
      return a.concat(b);
    });

    // swap long/lat to lat/log
    var swappedCoords = coords.map(function (pair) {
      return pair.reverse();
    });

    // create polyline, assign color, bind popup, add to map
    bikeRouteII = L.polyline(swappedCoords, { color: '#044571' }).bindPopup('ItinIsère').addTo(map);

    callback();
  });
};

getItinisereRoute([45.1836656,5.703573], [45.195926,5.735935], 'FASTEST', function(){
  console.log('route completed');
})
~~~

And the results again:

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="EyVmEb" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<!-- end codepen embed -->

# App Build

All those parts are great, but we need to pull them together now... We will need to create buttons and inputs to trigger each of the steps: get location, get address, get métromobilité route, get itinisère route.

We are going to use bootstrap to create a nice navbar, and put in buttons for each of the actions.

Let's reference the bootstrap libraries (we are going to use a pre-styled build of bootstrap, provided by [bootswatch](https://bootswatch.com/)).

Your <head> of index.html should look like this:

~~~markdown
<head>
  <meta charset=utf-8 />
  <title>VéloIsère</title>
  <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
  <link href='https://maxcdn.bootstrapcdn.com/bootswatch/3.3.6/superhero/bootstrap.min.css' rel='stylesheet' />
  <link href='https://api.mapbox.com/mapbox.js/v2.4.0/mapbox.css' rel='stylesheet' />
  <link href='style.css' rel='stylesheet' />
  <script src='https://cdnjs.cloudflare.com/ajax/libs/jquery/2.0.0/jquery.min.js'></script>
  <script src='https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.6/js/bootstrap.min.js'></script>
  <script src='https://rawgithub.com/mapbox/polyline/master/src/polyline.js'></script>
  <script src='https://rawgithub.com/mapbox/wellknown/master/wellknown.js'></script>
  <script src='https://api.mapbox.com/mapbox.js/v2.4.0/mapbox.js'></script>
</head>
~~~

To add in the navbar and action buttons, replace your <body> block with this:

~~~markdown
<body>
  <div class="navbar navbar-default navbar-static-top">
    <div class="container">
      <div class="navbar-header">
        <div class="navbar-brand">
          VéloIsère
        </div>

        <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navHeaderCollapse">
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </button>
      </div>
      <div class="collapse navbar-collapse navHeaderCollapse">
        <ul class="nav navbar-nav navbar-right">
          <li><button id="get_position" type="button" class="btn btn-primary">Get position</button></li>
          <li><form class="navbar-form" role="search">
            <div class="form-group">
              <input id="address_search" type="text" class="form-control" placeholder="Search">
            </div>
            <button id="get_address" class="btn btn-success" onclick="return false;">Get address</button>
          </form></li>
          <li class="dropdown">
            <button id="route-menu" class="dropdown-toggle btn btn-info" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false" disabled>Get route <span class="caret"></span></button>
            <ul class="dropdown-menu">
              <li><button id="II-route" class="menu-btn btn btn-info">Itinisère</button></li>
              <li><button id="MM-route" class="menu-btn btn btn-info">Métromobilité</button></li>
            </ul>
          </li>
        </ul>
      </div>
    </div>
  </div>

  <div id="map"></div>
  <script src="script.js"></script>
</body>
~~~

and your style.css file should now look like this:

~~~css
body {
  margin: 0;
  padding: 0;
}
.navbar {
  z-index: 2000;
}
.menu-btn {
  width: 100%;
}
#map {
  position: absolute;
  top: 41px;
  bottom: 0;
  width: 100%;
}
~~~

To trigger actions, we are going to use jQuery's 'click' binding. We tell the application that on click of a certain element, do something.

~~~javascript
$('#element_id').click(function () {
  // do somrthing now!
}
~~~

Add this to the bottom of your script.js file:

~~~javascript
// function to close mobile nav menu
function closeNav() {
  $('.navHeaderCollapse').collapse('hide');
};

// jQuery click actions
// on get address click
$('#get_address').click(function () {
  // remove any routes
  clearRoutes();

  // get value of search box
  var search = $('#address_search').val();

  // call address function
  getBanAddressCoords(search, pos[0] || 45.186502, pos[1] || 5.736339, function () {
    $('#route-menu').prop('disabled', false);
  });
});

// on get location button click
$('#get_position').click(function () {
  // remove any routes
  clearRoutes();
  getUserLocation();
});

// on dropdown select, get the route
$('#MM-route').click(function () {
  getMetromobiliteRoute(pos, addressPos, function () {
    closeNav();
  });
});

// on dropdown select, get the route
$('#II-route').click(function () {
  getItinisereRoute(pos, addressPos, 'FASTEST', function () {
    closeNav();
  });
});
~~~

Now we have an app! (Try firefox to use the geolocation...)

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="rLOmQG" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<!-- end codepen embed -->

## Fin !
