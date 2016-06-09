---
layout: tut
title: "Vélo Isère"
date: 2016-06-09 12:00:00
author: abenrob
published: true
posttype: tutorial
lang: fr
categories:
- fr
---
Dans ce tutoriel nous allons explorer les caractéristiques d'APIs de calcul d'itinéraire et construire une application simple pour trouver un itinéraire vélo entre deux points. Nous allons utiliser la géolocalisation par HTML5 pour indiquer notre localisation. La Base Adresse Nationale [BAN](https://adresse.data.gouv.fr/api/ ) nous permettra de trouver notre destination et les sites  [itinIsère](http://www.itinisere.fr/fr/api-open-services/169/OpenData/openservices) et [Métromobilité](http://www.metromobilite.fr/pages/opendata/OpenDataApi.html) nous aiderons à trouver les itinéraires.
<!--more-->

##Avant de commencer
Pour bien suivre ce tutoriel vous aurez besoin d'un éditeur de texte (comme Notepad++ ou Atom). Pas de problème si vous souhaitez utiliser le vôtre mais si vous n'en n'avez pas d'installé, vous pouvez télécharger  [Atom Editor](https://atom.io/).
Vous aurez également besoin de lancer un serveur web en local sur votre machine pour accéder à des ressources distantes comme des APIs. La manière la plus simple de le faire si vous avez Python installé est de naviguer avec la console à la racine du projet et de taper la commande  ```python -m SimpleHTTPServer```. Cela activera une page web à l'adresse suivante :  [http://localhost:8000](http://localhost:8000) .

# Démarrage

## Sites web
Au cours de cet atelier, nous allons construire une application web simple. Pour commencer, nous devons donc évoquer certains éléments d'un site web.

Un site web simple est composé de fichiers HTML, CSS et Javascript. Le HTML (Hyper Text Markup Language) fournit la structure de la page - il indique la position de chaque élément et fait référence aux autres composants de la page. Les CSS (Cascading Style Sheets) forment les règles de style qui sont mises en place pour décrire comment les éléments HTML doivent apparaître. Javascript est un langage de programmation utilisé par les sites web pour *agir* - recevoir des données, gérer les interactions, manipuler les données, etc. [jQuery](https://jquery.com/)  est une bibliothèque Javascript qui facilite grandement la manipulation du document, la gestion des événements et l'accès aux données. [Bootstrap](http://getbootstrap.com/) est un "framework", fait de CSS et de Javascript, qui permet de façonner un site web plus facilement, sans écrire les fichiers de style par soi-même. Ces ressources sont très utiles pour prototyper un site, ou donner rapidement un aspect soigné à un tutoriel ;)

## Une carte web simple
Nous allons utiliser [MapboxJS](https://www.mapbox.com/mapbox.js/api/v2.4.0/) (qui est une extension de [LeafletJS](http://leafletjs.com/)) pour mettre en place la carte dans l'application en très peu de lignes de code.

A la racine de notre projet, nous allons créer un fichier nommé 'index.html', un second nommé 'style.css' et un troisième nommé 'script.js'.

Dans index.html nous plaçons ce contenu :


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

Dans la balise <head>, vous noterez les références à notre style.css, ainsi qu'à mapbox.js. La référence à 'script.js' est située sous le conteneur de la carte (<div id="map"></div>) pour que la bibliothèque de cartographie ne soit pas appelée avant que le conteneur de la carte soit affiché.

Placez ce contenu dans style.css :

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

Nous précisons ici que la page et le conteneur de la carte doivent prendre toute la largeur et toute la hauteur.

Et placez ce contenu dans script.js :

~~~javascript
// define mapbox token for map creation
L.mapbox.accessToken = 'pk.eyJ1IjoiYWJlbnJvYiIsImEiOiJEYmh3WWNJIn0.fus8CLBKPBHDvSxiayhJyg';

// set up map
var map = L.mapbox.map('map', 'mapbox.streets')
    .setView([45.186502, 5.736339], 13);
~~~

Ici nous fournissons notre jeton (mapbox est un service soumis à abonnement, avec un premier niveau gratuit) et nous appelons la bibliothèque mapbox pour créer une carte avec le style 'streets' centrée sur Grenoble. Cette carte sera assignée à l'élément HTML dont l'identifiant (id) est 'map'.

Démarrez votre serveur web et admirez la page que nous avons créé :

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="YWXdPN" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>
<!-- end codepen embed -->

# Géolocalisation
La géolocalisation nous permet de connaître la position de l'utilisateur (ses coordonnées géographiques), elle fait partie de la spécification HTML5. Comme nous avons besoin d'un point de départ pour notre itinéraire, nous allons utiliser cette géolocalisation.

L'objet géolocalisation fait partie des objets du navigateur. La plupart des navigateurs modernes prennent en charge la géolocalisation, mais à partir de la version 50, Chrome (avec probablement d'autres navigateurs) n'en permet plus l'accès depuis une origine non sécurisée. Cela signifie qu'un site public sans SSL (HTTP, pas HTTPS) ne peut plus utiliser la géolocalisation dans Chrome. Heureusement pour nous, Firefox le permet toujours, et localhost est une origine sécurisée, donc le tutoriel devrait fonctionner même sur Chrome.

Ajoutez à script.js :

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

Attention à l'usage des fonctions de "callback" (geoSuccess and geoError dans ce cas). Les fonctions en javascript peuvent être soit *synchrones* soit *asynchrones*. Les fonctions synchrones sont exécutées entièrement avant de permettre le passage à l'étape suivante. Cela ne pose pas de problème dans beaucoup de cas car la durée d'exécution de beaucoup de fonctions est négligeable. Mais pour des fonctions plus lourdes (surtout celles qui ont besoin d'aller chercher des données), on ne souhaite pas que l'application ait finit d'obtenir toutes ses données avant de passer à la suite. C'est pourquoi on demande à l'application d'obtenir ses premières données et de les traiter dès leur réception tout en continuant à exécuter le script.

Et les résultats (essayez firefox, chrome 50+ ne function plus ):

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="pbJYZK" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<!-- end codepen embed -->

#APIs
Une API (ou interface de programmation) est un ensemble d'instructions que nous pouvons utiliser pour communiquer avec une application. Récemment, on utilise le terme API pour décrire un ensemble d'URLs qui peuvent être utilisées pour créer, lire, mettre à jour et supprimer des données distantes. Dans notre cas, nous voulons seulement récupérer des données (adresses et itinéraires) au format [JSON](https://fr.wikipedia.org/wiki/JavaScript_Object_Notation) (JavaScript Object Notation). Ce format humainement lisible permet de décrire des structures des données tout en restant facile à utiliser par l’interpréteur Javascript.

Pour accéder à ces données distantes, nous allons utiliser [jQuery](https://jquery.com/), une bibliothèque javascript qui aide à traverser le HTML, gérer les événements, et accéder aux données avec [Ajax](https://fr.wikipedia.org/wiki/Ajax_(informatique)) (asynchronous javascript and xml, mais utile pour JSON aussi). jQuery a une classe spécifique pour ajax+json [getJSON](http://api.jquery.com/jquery.getjson/) que nous allons utiliser pour chercher nos données. getJSON s'utilise comme ça :

~~~javascript
$.getJSON( 'url/des/données/', function( json ) {
  // partie asynchronome
  console.log('Regardez mes données: ' + json.dataname);
 });
~~~

# Adresses
Afin d'obtenir un itinéraire nous avons besoin de deux points : départ et arrivée. Nous avons le point de départ (notre géolocalisation) et pour l'arrivée nous allons utiliser la Basse Adresse Nationale : [BAN](https://adresse.data.gouv.fr/)

Nous allons utiliser également la fonction getJSON et afficher les résultats sur la console afin de les explorer. (vous pouvez supprimer la ligne "getUserLocation();" dans script.js, nous l'utiliserons plus tard).

Ajoutez ensuite JQuery à index.html, juste après la référence à style.css

~~~markdown
<script src='https://cdnjs.cloudflare.com/ajax/libs/jquery/2.0.0/jquery.min.js'></script>
~~~

Rechargez votre page et ouvrez ensuite la console [voir cette question sur Stackoverflow pour un peu d'aide](http://webmasters.stackexchange.com/questions/8525/how-to-open-the-javascript-console-in-different-browsers).

Collez ensuite ce qui suit et appuyez sur entrée.

~~~javascript
$.getJSON('https://api-adresse.data.gouv.fr/search/?q=31 rue gustave eiffel', function (data) {
  console.log(data);
});
~~~

Nous pouvons voir les résultats ici (chaque item est une adresse trouvée) :

<!-- json embed -->
<div id="getJson1" class="btn btn-sm btn-primary">obtenir JSON</div>
<div id="json1" class="well json-block"></div>
<script>$('#getJson1').click(function() {
  $.getJSON('https://api-adresse.data.gouv.fr/search/?q=31 rue gustave eiffel', function (data) {
    document.getElementById("json1").innerHTML = JSONTree.create(data);
  });
});</script>
<!-- end json embed -->

L'API de la BAN nous permet d'apporter une précision sur la localisation de nos résultats en fournissant en premier les adresses correspondantes les plus proches. Essayez le même code que précédemment dans la console (pour cela appuyez sur la flèche du haut pour accéder aux commandes précédentes dans la console) mais en ajoutant latitude, longitude à la fin de l'URL de requête comme suit :  ```&lat=45.186502&lon=5.736339``` Vous pouvez voir comme le résultat Grenoble arrive en premier ?

Construisons maintenant une fonction pour placer un point sur la carte correspondant à une adresse recherchée.

Ajoutez à script.js :

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

Et les résultats :

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="EyVjbb" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<!-- end codepen embed -->

# Itinéraires
Maintenant que l'on sait obtenir les points de départ et d'arrivée, nous pouvons maintenant explorer les APIs d'itinéraires

## Métromobilité
L'[API Métromobilité](http://www.metromobilite.fr/pages/opendata/OpenDataApi.html) a une ressource [Horaires OTP](http://www.metromobilite.fr/pages/opendata/OpenDataApi.html#Otp), qui utilise [OpenTripPlanner](http://www.opentripplanner.org/) pour fournir les itinéraires multimodaux avec leur données. Pour chercher un itinéraire vélo, il faut fournir les coordonnées de départ, celles de l'arrivé et le mode de transport, qui pour nous sera 'BICYCLE'.

Nous allons essayer. Collez ensuite ce qui suit et appuyez sur entrée.

~~~javascript
var results;
$.getJSON('http://data.metromobilite.fr/otp/routers/default/plan?mode=BICYCLE&fromPlace=45.1836656,5.703573&toPlace=45.195926,5.735935', function (data) {
  console.log(data);results = data;
});
~~~

Nous pouvons voir les résultats ici (les itinéraires sont dans plan.itineraries) :

<!-- json embed -->
<div id="getJson2" class="btn btn-sm btn-primary">obtenir JSON</div>
<div id="json2" class="well json-block"></div>
<script>$('#getJson2').click(function() {
  $.getJSON('http://data.metromobilite.fr/otp/routers/default/plan?mode=BICYCLE&fromPlace=45.1836656,5.703573&toPlace=45.195926,5.735935', function (data) {
    document.getElementById("json2").innerHTML = JSONTree.create(data);
  });
});</script>
<!-- end json embed -->

Pour la partie cartographique, nous voulons la géométrie du trajet. Comme le résultat a été affecté a une variable, nous pouvons jouer avec les données dans la console. Pour voir le tracé de l'itinéraire, tapez ```results.plan.itineraries[0].legs[0].legGeometry.points``` dans la console. Nous voyons une chaîne de caractères encodée, qui contient le tracé. Voyez aussi ```results.plan.itineraries[0].legs[0].steps``` pour voir les coordonnées et instructions de direction.

Attendez ! Ce tracé était n'importe quoi ! Pour rendre le tracé utile, il faut le décoder. Mapbox a créé une bibliothèque pour faire cela : [polyline](https://github.com/mapbox/polyline).

Ajoutez ensuite polyline à index.html, juste après la référence à jQuery

~~~markdown
<script src='https://rawgithub.com/mapbox/polyline/master/src/polyline.js'></script>
~~~

Dans script.js, vous pouvez effacer l'appel de la function getBanAddressCoords(). On mettra ceci à la fin du fichier :

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

Ce qui donne comme résultat :

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="gMamNd" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<!-- end codepen embed -->

## ItinIsère
L' [API ItinIsère](http://www.itinisere.fr/fr/api-open-services/169/OpenData/openservicesml) propose une fonction appelée [JourneyPlanner/v2/BikeTrip/json](http://www.itinisere.fr/fr/api-open-services/169/OpenData/openservices#!/journeyplanner/v2/BikeTripJsonGET_get_1) que l'on peut utiliser pour obtenir des itinéraires vélo à partir des données ItinIsère. Pour obtenir un itinéraire vélo nous devons fournir des coordonnées de départ, d'arrivée, une date de départ, un horaire de départ et un algorithme. Dans notre cas nous allons utiliser l'algorithme 'FASTEST'. Itinisère requiert une clé d'authentification pour pouvoir être utilisée, vous pouvez utiliser la clé Maptime-Alpes, mais vous devrez demander la vôtre pour vos propres développement futurs.

Essayons en copiant/collant dans la console :

~~~javascript
var results;
$.getJSON('http://www.itinisere.fr/webServices/TransinfoService/api/journeyplanner/v2/BikeTrip/json?DepLat=45.1837081&DepLon=5.7035291&ArrLat=45.195926&ArrLon=5.735935&Date=2016-06-07&DepartureTime=10-48&user_key=0016bf2ff47f630bab2e65bba954c091&Algorithm=FASTEST&callback&callback=?', function (data) {
  console.log(data);results = data;
});
~~~

On peut voir les résultats ci-après (les segments de l'itinéraire sont dans trips.Trip[0].sections.Section[0].Leg.pathLinks.PathLink):

<!-- json embed -->
<div id="getJson3" class="btn btn-sm btn-primary">obtenir JSON</div>
<div id="json3" class="well json-block"></div>
<script>$('#getJson3').click(function() {
  $.getJSON('http://www.itinisere.fr/webServices/TransinfoService/api/journeyplanner/v2/BikeTrip/json?DepLat=45.1837081&DepLon=5.7035291&ArrLat=45.195926&ArrLon=5.735935&Date=2016-06-07&DepartureTime=10-48&user_key=0016bf2ff47f630bab2e65bba954c091&Algorithm=FASTEST&callback&callback=?', function (data) {
    document.getElementById("json3").innerHTML = JSONTree.create(data);
  });
});</script>
<!-- end json embed -->

Là encore, pour l'exercice de cartographie, on souhaite le tracé du parcours. Pour voir la forme géométrique, tapez  ```results.trips.Trip[0].sections.Section[0].Leg.pathLinks.PathLink[0]``` dans la console. On obtient dans le champ "Geometry" une chaîne de caractères de type "LINESTRING ()" et qui contient le tracé spécifique du segment d'itinéraire. Nous obtenons également les instructions de direction du segment qui doivent être fournies à l'utilisateur.

Le tracé est présenté au format  [WKT (Well-Known text)](https://en.wikipedia.org/wiki/Well-known_text). Pour l'utiliser dans notre carte nous avons besoin de convertir le WKT dans un format lisible pour la carte. Mapbox (encore) a créé une bibliothèque  ([wellknown](https://github.com/mapbox/wellknown)) pour convertir le WKT en geoJSON mais cela nous oblige à avoir ces caractéristiques pour chaque segment alors que l'on souhaite un simple tracé. En utilisant les fonctions  javascript *map* et *reduce* on peut convertir le WKT avec *map*, combiner tous les segments en une ligne avec *reduce* et ensuite intervertir [longitude,latitude] en [latitude.longitude] avec un autre *map*.

Ajoutez ensuite wellknown à index.html, juste après la référence à polyline.

~~~markdown
<script src='https://rawgithub.com/mapbox/wellknown/master/wellknown.js'></script>
~~~

Dans script.js (vous pouvez enlever si vous préférez l'appel de getMetromobiliteRoute() ) on mettra ceci à la fin du fichier :

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

Ce qui donne comme résultat :

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="EyVmEb" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<!-- end codepen embed -->

#Assemblage

Maintenant que toutes ces parties ont été exécutées nous avons besoin maintenant de les assembler. Il nous faut créer les boutons et entrées qui vont déclencher chacune des étapes : get location, get address, get métromobilité route, get itinisère route.

Nous utiliserons les bibliothèques Bootstrap pour créer une barre de navigation sympathique et appliquer un thème aux boutons pour chacune des actions.
Pour retrouvez les librairies Bootstrap : nous utiliserons une version déjà stylisée de Bootstrap, fournie par [bootswatch](https://bootswatch.com/)).

La balise <head> du fichier index.html devra ressembler à ça :

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

Pour ajouter la barre de navigation, remplacez votre balise <body> par ceci :

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

Votre style.css doit ressembler à ceci :

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

Pour déclencher les actions nous allons utiliser une fonction jQuery qui réagit au click : Quand on clique sur un élémént en particulier, l'application lance une action particulière.

~~~javascript
$('#element_id').click(function () {
  // fait qqch maintenant !
}
~~~

Ajoutez ceci à la fin du fichier script.js :

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

Nous avons une vraie application ! (Essayez avec firefox pour le coté  géolocalisation)

<!-- codepen embed -->
<p data-height="500" data-theme-id="0" data-slug-hash="rLOmQG" data-default-tab="result" data-user="abenrob" data-embed-version="2" data-preview="true" class="codepen"></p>
<!-- end codepen embed -->

## Fin !
