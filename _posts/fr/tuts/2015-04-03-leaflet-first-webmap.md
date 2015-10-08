---
layout: tut
title: "Créer une carte avec leaflet"
date: 2015-04-04 10:48:00
author: tsamaya
published: true
posttype: tutorial
lang: fr
categories:
- fr
---
Cet article fait suite à la [présentation](http://maptime-alpes.com/leaflet-first-webmap/) "Débuter avec Leaflet" lors du Meet-Up [Maptime-Alpes](http://maptime-alpes.com) de février.
<!--more-->

Nous allons donc créer une webmap avec [leaflet](http://leafletjs.com).

Tout d'abord nous allons inclure la librairie et sa feuille de style:

{% highlight javascript %}

<link rel="stylesheet" href="//cdn.leafletjs.com/leaflet-0.7.3/leaflet.css" />

<script src="//cdn.leafletjs.com/leaflet-0.7.3/leaflet.js"> </script>

{% endhighlight %}

L'ajout de la carte s'effectue avec la simple syntaxe suivante:

{% highlight javascript %}

    var map = L.map('leaflet-map').setView([45.18, 5.72], 13);

{% endhighlight %}

la carte est centrée sur les coordonnées Latitude 45.18 N et Longitude 5.72 E. Des coordonnées positives indiquent des coordonées Nord et Est, les coordonées sont négatives au Sud et à l'Ouest.

Ajoutons maintenant un fond de plan, il s'agit d'une carte dont les tuiles (images de 256x256 ont été calculées) sont hébergées sur un serveur:

{% highlight javascript %}
    L.tileLayer('http://{s}.tile.stamen.com/watercolor/{z}/{x}/{y}.png').addTo(map);

{% endhighlight %}

Nous utilisons ici une fond de plan d'une startup de San Franciso dont le style est original.

Précisions maintenant les atributs particuliers de notre URL:
- {s} représente les sous domaines, il s'agit de redondances des serveurs, les images sont copiées sur différents serveurs pour répartir la charge.
- {z} niveau d'échelle des tuiles
- {x} et {y} sont, bien sûr, l'abscisse et l'ordonnée.
Ces infomrations sont décrites dans la [présentation](http://maptime-alpes.com/leaflet-first-webmap/).

L'ajout d'un marqueur à l'adresse de CoWork in Grenoble où nous sommes ce soir s'effectue ainsi:

{% highlight javascript %}

    L.marker([45.19129, 5.73332]).bindPopup("Cowork In Grenoble").addTo(map);

{% endhighlight %}


Et voici le résultat

<a class="jsbin-embed" href="http://jsbin.com/dezopu/embed?output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script>


Maintenant passons au deuxième exercice. Pour commencer nous ajoutons plus de marqueurs, mais au lieu de les ajouter à la carte directmenet, nous les ajoutons à un groupe `LayerGroup`, ainsi ce groupe ajouté à un Control permet de changer l'affichage à l'aide de case à cocher, permettant ainsi d'afficher ou masquer des couches de la carte.

{% highlight javascript %}

    var poi = new L.LayerGroup();
    L.marker([45.17447, 5.54346]).bindPopup('Autrans').addTo(cities);
    L.marker([45.22201, 5.8037]).bindPopup('Montbonnot Saint-Martin').addTo(cities);
    L.marker([45.0781, 5.77078]).bindPopup('Vizille').addTo(cities);

{% endhighlight %}

Enfin nous allons aussi ajouter la possiblité de changer de fond de plan

{% highlight javascript %}

    var mbAttr = 'Map data &copy; <a href="http://openstreetmap.org">OpenStreetMap</a> contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="http://mapbox.com">Mapbox</a>',
      mbUrl = 'https://{s}.tiles.mapbox.com/v3/{id}/{z}/{x}/{y}.png';

    var grayscale = L.tileLayer(mbUrl, {
        id: 'examples.map-20v6611k',
        attribution: mbAttr
      }),
      streets = L.tileLayer(mbUrl, {
        id: 'examples.map-i875mjb7',
        attribution: mbAttr
      }),
      imagery = L.tileLayer('http://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}'),
      toner = L.tileLayer('http://{s}.tile.stamen.com/toner/{z}/{x}/{y}.png'),
      space = L.tileLayer('http://{s}.tiles.mapbox.com/v3/eleanor.ipncow29/{z}/{x}/{y}.png');

{% endhighlight %}

Et pour finir modifions la carte, et ajoutons lui les groupes et les fonds de plans:

{% highlight javascript %}

    var map = L.map('leaflet-map', {
      center: [45.19129, 5.73332],
      zoom: 10,
      layers: [grayscale, poi]
    });

    var baseLayers = {
      "Grayscale": grayscale,
      "Streets": streets,
      "Stamen Toner": toner,
      "esri imagery": imagery,
      "MapBox space": space
    };

    var overlays = {
      "POI": poi
    };

    L.control.layers(baseLayers, overlays).addTo(map);
{% endhighlight %}

Cela donne cela:

<a class="jsbin-embed" href="http://jsbin.com/cemimo/embed?output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script>

Notre premiere carte est plutôt jolie !

##Challenges

###Délits
nous allons réaliser une carte des délist sur San Francisco, pour cela nous utilisons le service [sanfrancisco.crimespotting](http://sanfrancisco.crimespotting.org/crime-data?format=json&count=1000&dstart=2014-12-01) retournant des données au format geojson; 

<a class="jsbin-embed" href="http://jsbin.com/xiwiqa/embed?output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script>

###Seismes
nous passons à l'étape supérieur en réalisant une carte des seisme récent. pour cela nous disposns du service [USGS](http://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson) retournant les données aux format geojson. L'affichage de tous les points n'étant pas lisible, nous utilisons cette fois la carte de chaleur, ainsi la concentration des points indique les zones où le nombre de trembelement de terre est  impsrtant.

<a class="jsbin-embed" href="http://jsbin.com/jolego/embed?output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script>
