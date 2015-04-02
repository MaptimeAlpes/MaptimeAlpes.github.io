---
layout: tut
title: "d3 Geospatial"
date: 2015-04-02 12:15:00
author: abenrob
published: true
posttype: tutorial
lang: fr
categories:
- fr
---
Une simple recherche sur internet vous indiquera que, [D3](http://d3js.org/) est une librairie dédiée à la visualisation des données fonctionnant à merveilles dans votre navigateur. Vous entendez *visualisation des données* et pensez *je n'ai pas besoin d'un lib de viz des données, je fais des cartes. Et bien , les cartes reposent sur des données, de plsu D3 est vraiement doué avec les cartes, nous garderons cela en tête. Pourquoi choisir D3 plutôt qu'une autre librairie comme [Leaflet.js](http://leafletjs.com/) par exemple. La réponse immédiate est que cela dépend du cas d'usage et de votre besoin d'afficahge. Dans ce tutoriel nous allons étudier plus amplement la réponse à apporter.

D3 signifie  **Documents Dirigés par les Données (Data Driven Documents)**. Ainsi, nous allons découvrir cela en trois parties. Premièrement, nous allons regarder l'aspect **Documents**, puis l'aspect **Données** et enfin explorer l'aspect **Dirigés**. Au moment de nous plonger dans le vif du sujet de ce tutoriel, vous devriez avec une idée assez claire de la façon dont D3 se positionne par rapport à ce que vous connaisez jusqu'à présent.
<!--more-->
## Avant propos

Pour participer au mieux à ce tutoriel vous avez besion d'un éditeur de texte(comme Notepad++, Atom ou Sublime Text). Utiliser votre éditeur favoris, ou si vous n'en avez pas télépcharger [Sublime Text](http://www.sublimetext.com/).

# Documents

Au coeur de la librairie, D3 utlise les inforamtions et les transforme pour les afficher. Cet affichage est le document, et à toute fin pratique, le document est un [SVG](http://www.w3.org/TR/SVG/). Scalable Vector Graphics est un format de fichier encodant les informations vectorielle pour des usages multiples, incluant le naviagteur web. Les SVG sont utilisés partour pour afficher toutes sortes de données. Si vous avez déjà exporté une carte depuis [QGIS](http://www.qgis.org/en/site/) et stylisé celle-ci dans une [application graphique](https://inkscape.org/en/), les données ont été stockées en SVG à certaines étapes du processus.

SVG sont lisibles, ce qui est bien pour nous : car nous ne sommes pas des machines. Ceci est un SVG:

~~~markup
<svg width="720" height="120">
  <circle cx="40" cy="60" r="10"></circle>
  <circle cx="80" cy="60" r="10"></circle>
  <circle cx="120" cy="60" r="10"></circle>
</svg>
~~~

Cet [exemple de cercles](http://bl.ocks.org/powersa/raw/dae5b2e58c5a813208f5/) utilise le même document embarqué dans une page web. Faites un clic droit sur l'un des cercles et sélectionner *Inspecter l'élément* du menu contextuel.

Chacun de ces cercles est un élement du SVG, qui a une largeur et une hauteur. C'est le type de document que D3 produit dans le navigateur. On peut indiquer : ajouter des cercle, déplace des cercles et supprimer ces cercles. Regarder ce tutoriel sur la [sélection](http://bost.ocks.org/mike/circles/) pour approfondir le sujet. Il est également intéressant de noter que D3 a la possibilité d'écrire et d'éditer de nombreux types de formes, pas seulement des cercles!

# Données

Donc, nous avons un outil qui peut écrire un SVG dans le navigateur... Cela ne va pas vraiment faire quelque chose, a moins que nous ayons quelque chose que vous voulez dessiner. En D3, ce que *quelque chose* est presque toujours basé sur des données.

Regardons ce [diagramme de dispersion](http://bl.ocks.org/mbostock/3887118). Ce type de visualisation est famillère et facile à comprendre. *Sepal Width* est placé sur l'axe des x et *Sepal Length* est placé sur l'axe des y. D3 dessine l'ensemble du graphique. Mais comment D3 sait quoi et où le dessiner ? Faites défiler la page jusqu'à ce que vous voyez [**data.tsv**](http://bl.ocks.org/mbostock/3887118#data.tsv) (ou grâce à un clic sur ce lien). Quand la page web se charge, D3 lit ce fichier et ajoute chaque enregistrement comme un cercle dans le diagramme de dispersion. les coordonnées et couleur des cercles sont définies dans les enregistrements.

# Dirigés

At this point *Driven* might not make sense. If we stopped here, we could probably make a case to change *Driven* to *Defined*. Let's keep going. 

**Driven** is actually one of the defining characteristics of D3. Remember the work flow we discussed where you export an SVG from desktop GIS to do custom design work in a graphics editor? As soon as your vector data leaves the GIS, features lose the data that defines them. At best, the great attributes you honed are represented graphically and at worst are totally gone.

That type of thing doesn't happen in D3. Not only does your data define the elements in your SVG, the data is also bound (joined) to the elements in your document. A circle isn't just a circle element with an x,y and radius, it's also the data that originated the element in the first place. This characteristic of D3 allows data to drive your visualization, not only upon creation, but throughout its life cycle.

# Conseils

* La courbe d'apprentissage peut être assez raide. Restez positif
* Commencez simplement, ajouter de la complexité petit à petit
* Reportez vous sur la documentation les tutoriels
* **Cannibaliser le code** où et quand vous pouvez. D3 dispose de très bons exempleset la plupart sont accessible gratuitement. Quelque chose vous intéresse ? Regarder comment cela a été fait.

# Début du tutoriel!

Au moment où nous terminons ce tutoriel, nous aurons construit notre première (ou énième) carte avec D3! Ce ne sera pas la carte la plus jolie que vous avez jamais fait, mais j'espère qu'une fois que vous l'aurez  fait, vous aurez une rampe de lancement pour faire encore mieux avec D3 à l'avenir.

## Etape 1: Web Pages

Si vous avez besoin d'aidepour les compsants d'une page web, consultez ce [tutoriel sur les webmap](http://maptimesea.github.io/2014/11/05/web-map-intro.html#let-s-get-started). A partir de là, nous allons suivre les étapes pour partir d'une page vierge et obtenir notre modèle *map.html*. 

A ce stade, vous devriez avoir un fichier *map.html* sur votre poste que vous pouvez ouvrir dans votre navigateur Web. Ouvrez ce fichier *map.html* dans votre navigateur Web. Ce  fichier ressemble à ceci:

~~~markup
<!doctype html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <style>
    body {
      background-color: green;
    }
  </style>

</head>

<body>
  <script>
    var bestPlaceEver = "maptime";
  </script>
</body>
</html>
~~~
{: .line-numbers}

Prenez soin de vos yeux. N'hésitez pas à changer la couleur de fond en blanc:

~~~css
  body {
    background-color: white;
  }
~~~

### Ajoutez quelques librairies javascript 

Pour que notre code fasse des choses impressionnantes, nous allons utiliser des outils existants (d3 est l'un d'eux)
Maintenant, vous pouvez insérer dans votre page D3. Pour l'instant, nous allons utiliser une version hébergée au lieu de le copier localement. Nous allons également ajouter la bibliothèque pour travailler avec topojson. Ajouter cela directement après la balise `<head>` de `map.html`.

~~~markup
  <script src="http://d3js.org/d3.v3.min.js"></script>
  <script src="http://d3js.org/topojson.v0.min.js"></script>
~~~

## Etape 2: Construction du document

Avant d'ajouter une carte à notre page, nous allons lui préparer un emplacement. Ajoutez les lignes suivantes à l'intérieur de la section sript:

### Définir la largeur et la hauteur de votre graphique

~~~javascript
  var width = 960,
      height = 480;
~~~

### Créér votre SVG

~~~javascript
  var svg = d3.select("body").append("svg")
        .attr("width", width)
        .attr("height", height);
~~~

Ce morceau de code indique à votre navigateur que la variable *svg* est un élément SVG sous la balise body. Si SVG n'existe pas, D3 en crée un. Ajoutez à cela *map.html* et rechargez la page. Faites un clic droit dans la partie supérieure gauche de la page. Sélectionnez *Inspectez l'élement*, et vous verrez que votre page contient un élément SVG vide.

### Définir votre projection

~~~javascript
  var projection = d3.geo.equirectangular()
        .scale(153) // scales your map
        .translate([width / 2, height / 2]); // centers in SVG
~~~

When you define a projection, you tell D3 how to transform your data from spherical to Cartesian coordinates. Take a look at the [projection docs](https://github.com/mbostock/d3/wiki/Geo-Projections) to get a sense of how you can project your data with D3. 

### Translate to screen coordinates

~~~javascript
  var path = d3.geo.path()
        .projection(projection);
~~~

Lorsque D3 déssine votre SVG, it traduit les coordonnées géographique en coordonnées de pixel sur l'écran. Cette fonctionnalité est appelé *path generator*. Dans ce block, nous stockons le path dans la variable *path* afin d'y accéder tout au long de notre code.
A cette étape, nous avons un SVG vide sur notre page. Notre **document** est prêt, nous avons besoin des **données**.

## Etape 3: Ajouter les données

The data we'll use for this map is stored in the TopoJSON format. We won't address specifics here, but if you want to learn more about the spatial format TopoJSON, take a look at the [wiki](https://github.com/mbostock/topojson/wiki).

Your data is waiting for you [here](https://gist.github.com/abenrob/ab3de11f64071ddc4f68). _(click "Download Gist")_

Download the data and unpack it! If you don't have software to unzip the data, you can also copy the raw contents to a file on your computer and save as *worldtopo.js*. Move *world-50m.json* to the same location as *map.html*.

## STEP 4: Load the data 

If you look at the beginning of the *worldtopo.js* file, you will see

~~~javascript
var worldtopo = {...}
~~~

we went ahead and defined the data as a javascript variable, so you wouldn't need a webserver to make this tutorial work. To use this data, we need to load it as a script. Just below the two other libraries, we'll add one more that references this file. That section should now look like this:

~~~markup
  <script src="http://d3js.org/d3.v3.min.js"></script>
  <script src="http://d3js.org/topojson.v0.min.js"></script>
  <script src="worldtopo.js"></script>
~~~

When you complete this next step step, you will have a map. Below your path generator, insert the following:

~~~javascript
  svg.append("path")
      .datum(topojson.object(worldtopo, worldtopo.objects.land))
      .attr("class", "land")
      .attr("d", path);
  svg.append("path")
      .datum(topojson.mesh(worldtopo, worldtopo.objects.countries, function(a, b) { return a.id !== b.id; }))
      .attr("class", "boundary")
      .attr("d", path);
~~~

This chunk of code goes through *worldtopo.js* and appends two fetaures (land and boundaries) to the path generator. This is a great time to *Inspect Element* on your web page or dig into the TopoJSON. As you can see, your SVG has a "path" element (shape) for each feature in your TopoJSON. See how data drives this document?

Maintenant votre carte ressemble à cela:

![bw map](/assets/img/tutorials/bw_map.png)

## Etape 5: Make it pretty

### ajouter des styles basiques

ajouter ces lignes à votre section `<style></style>` pour donner une jolie couleur bleue au fond et une bordure à l'élement svg, et pour colorier aussi les payes et les frontières.

~~~css
  svg {
    border: 2px solid black;
    background-color: #a4bac7;
  }
  .land {
    fill: #d7c7ad;
    stroke: #766951;
  }

  .boundary {
    fill: none;
    stroke: #a5967e;
  }
~~~

Plus joli:

![color map](/assets/img/tutorials/color_map.png)

### Add the graticule

Nous ne avons pas besoin d'un réticule, mais c'e 'est une chose disponible dans d3, nous allons donc l'utiliser!

insérer ce code de réticule au bas de votre section `<script></script>`

~~~javascript
  var graticule = d3.geo.graticule();

  svg.append("g")
    .attr("class", "graticule")
    .selectAll("path")
    .data(graticule.lines)
    .enter().append("path")
    .attr("d", path);
~~~

et ajouter ces styles à la section `<style></style>`

~~~css
  .graticule {
    fill: none;
    stroke: #fff;
    stroke-width: .5px;
  }

  .graticule :nth-child(2n) {
    stroke-dasharray: 2,2;
  }
~~~

Hé - cela ressemble à une vrai carte ! 

![graticule map](/assets/img/tutorials/grat_map.png)


**Félicitations!** Vous avez construit votre première webmap avec D3.js!

## STEP 6: Challenges

1. **Changer la translation**. Essayer .translate([400,600])  
2. **Changer l' échellee**. Essayer .scale(500)
3. **Changer la projection**. Essayer [autre chose](https://github.com/mbostock/d3/wiki/Geo-Projections). (this is a nice globe d3.geo.orthographic()!)
4. **Trouver les données**. Utiliser votre console Javascript pour accéder aux élements de données. Astuce: il s'agit d'une [sélection](https://github.com/mbostock/d3/wiki/Selections)
5. **Charger les données à partir d'un fichier** Essayer ce [TopoJSON](https://gist.github.com/abenrob/787723ca91772591b47e) - au lieu de le définir avec `var worldjson =` , charger le en utilisant [d3.json](https://github.com/mbostock/d3/wiki/Requests#d3_json). Exemples [ici](http://bost.ocks.org/mike/map/)
5. **Essayez de faire pivoter la projection** Voici une façon de mettre à jour la rotation en continue:

~~~javascript
  var lat = 0;
  setInterval(function(){
    lat = lat +.25
    projection.rotate([lat,0]);
    svg.selectAll(".land")
      .attr("d", path);
    svg.selectAll(".boundary")
      .attr("d", path);
    svg.selectAll(".graticule")
      .attr("d", path);
  },50);
~~~

# Ressources

- [Basic map example](http://bl.ocks.org/abenrob/27290f6dd43de81578af)
- [Rotating map example](http://bl.ocks.org/abenrob/2d7a99385e44db333da7)
- [JSON load example](http://bl.ocks.org/abenrob/c4ac3d581a7b16ff5f2f)

## Fin.

Tutoriel inspiré FORTEMENT de [MaptimeSea](http://maptimesea.github.io/2015/01/07/d3-mapping.html) et [maori.geek](http://www.maori.geek.nz/post/d3_js_geo_fun). Check out their other stuff!