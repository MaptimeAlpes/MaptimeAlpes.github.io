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
  <link href='https://maxcdn.bootstrapcdn.com/bootswatch/3.3.6/superhero/bootstrap.min.css' rel='stylesheet' />
  <link href='https://api.mapbox.com/mapbox.js/v2.4.0/mapbox.css' rel='stylesheet' />
  <link href='style.css' rel='stylesheet' />
  <script src='https://cdnjs.cloudflare.com/ajax/libs/jquery/2.0.0/jquery.min.js'></script>
  <script src='https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.6/js/bootstrap.min.js'></script>
  <script src='https://rawgithub.com/mapbox/polyline/master/src/polyline.js'></script>
  <script src='https://rawgithub.com/mapbox/wellknown/master/wellknown.js'></script>
  <script src='https://api.mapbox.com/mapbox.js/v2.4.0/mapbox.js'></script>
</head>

<body>
  <div id="map"></div>
  <script src="script.js"></script>
</body>

</html>
~~~

You will notice in the <head> section that we have included references to our style.css, as well as the libraries we are going to use in the workshop (jQuery, bootstrap, mapbox.js, as well as 2 data-helper libraries from mapbox - ployline and wellknown, which we will detail later on). Below the map container (<div id="map"></div>) we put our script.js reference, so the mapping library isn't called before the map container is rendered.

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

<p data-height="500" data-theme-id="0" data-slug-hash="YWXdPN" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/abenrob/pen/YWXdPN/">vélo isère step 1</a> by Adam Roberts (<a href="http://codepen.io/abenrob">@abenrob</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

# Tips

* The learning curve can be pretty steep. Stay positive
* Start simple, add complexity piece by piece
* Refer to documentation/tutorials
* **Cannibalize code** wherever/whenever you can. Mapbox has great examples and most the code is freely accessible. See something you like? Take a look at how it's done!
