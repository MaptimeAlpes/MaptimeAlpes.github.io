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
Une simple recherche sur internet vous indiquera que, [D3](http://d3js.org/) est une librairie dédiée à la visualisation des données fonctionnant à merveilles dans votre navigateur. Vous entendez *visualisation des données* et pensez *je n'ai pas besoin d'un lib de viz des données*, je fais des cartes. Et bien, les cartes reposent sur des données, de plus D3 est vraiement doué avec les cartes. Pourquoi choisir D3 plutôt qu'une autre librairie comme [Leaflet.js](http://leafletjs.com/) par exemple. La réponse immédiate est que cela dépend du cas d'usage et de votre besoin d'affichage. Dans ce tutoriel nous allons étudier plus amplement la réponse à apporter.

D3 signifie  **Documents Dirigés par les Données (Data Driven Documents)**. Ainsi, nous allons découvrir cela en trois parties. Premièrement, nous allons regarder l'aspect **Documents**, puis l'aspect **Données** et enfin explorer l'aspect **Dirigés**. Au moment de nous plonger dans le vif du sujet de ce tutoriel, vous devriez avec une idée assez claire de la façon dont D3 se positionne par rapport à ce que vous connaisez jusqu'à présent.

<!--more-->
## Avant propos

Pour participer au mieux à ce tutoriel vous avez besoin d'un éditeur de texte (comme Notepad++, Atom, Brackets ou Sublime Text). Utiliser votre éditeur favoris, ou si vous n'en avez pas télécharger [Sublime Text](http://www.sublimetext.com/).

# Documents

Au coeur de la librairie, D3 utlise les informations et les transforme pour les afficher. Cet affichage est le document, et à toute fin pratique, le document est un [SVG](http://www.w3.org/TR/SVG/). Scalable Vector Graphics est un format de fichier encodant les informations vectorielles pour des usages multiples, incluant le navigateur web. Les SVG sont utilisés partour pour afficher toutes sortes de données. Si vous avez déjà exporté une carte depuis [QGIS](http://www.qgis.org/en/site/) et stylisé celle-ci dans une [application graphique](https://inkscape.org/en/), les données ont été stockées en SVG à certaines étapes du processus.

Les SVG sont lisibles, ce qui est bien pour nous : car nous ne sommes pas des machines. Ceci est un SVG:

~~~markup
<svg width="720" height="120">
  <circle cx="40" cy="60" r="10"></circle>
  <circle cx="80" cy="60" r="10"></circle>
  <circle cx="120" cy="60" r="10"></circle>
</svg>
~~~

Cet [exemple de cercles](http://bl.ocks.org/powersa/raw/dae5b2e58c5a813208f5/) utilise le même document embarqué dans une page web. Faites un clic droit sur l'un des cercles et sélectionner *Inspecter l'élément* du menu contextuel.

Chacun de ces cercles est un élement du SVG, qui a une largeur et une hauteur. C'est le type de document que D3 produit dans le navigateur. On peut indiquer : ajoute des cercle, déplace des cercles et supprime ces cercles. Regarder ce tutoriel sur la [sélection](http://bost.ocks.org/mike/circles/) pour approfondir le sujet. Il est également intéressant de noter que D3 a la possibilité d'écrire et d'éditer de nombreux types de formes, pas seulement des cercles!

# Données

Donc, nous avons un outil qui peut écrire un SVG dans le navigateur... Cela ne va pas vraiment grand faire grand chose, a moins que nous ayons quelque chose à dessiner. En D3, ce *quelque chose* est presque toujours basé sur des données.

Regardons ce [diagramme de dispersion](http://bl.ocks.org/mbostock/3887118). Ce type de visualisation est famillère et facile à comprendre. *Sepal Width* est placé sur l'axe des x et *Sepal Length* est placé sur l'axe des y. D3 dessine l'ensemble du graphique. Mais comment D3 sait quoi et où le dessiner ? Faites défiler la page jusqu'à ce que vous voyez [**data.tsv**](http://bl.ocks.org/mbostock/3887118#data.tsv) (ou grâce à un clic sur ce lien). Quand la page se charge, D3 lit ce fichier et ajoute chaque enregistrement comme un cercle dans le diagramme de dispersion. Les coordonnées et couleurs des cercles sont définies dans les enregistrements.

# Dirigées

A ce stade *dirigées* n'a pas encore de sens. Si l'on s'arrête ici, nous pourrions probablement changer *Dirigées* par *Défini*. 

 **Dirigées** est en fait l'une des caractéristiques déterminantes de D3. Rappelez-vous le flux de travail, nous avons vu où exporter un SVG d'outil de SIG pour réalisé un travail personnalisé dans un outils de dessins. Dès que les données vectorielle quitte le SIG, les entités perdent les données qui les définissent. Au mieux, certains attributs sont représentées graphiquement et au pire ils ont totalement disparu.

Ce genre de chose ne arrive pas dans D3. Non seulement vos données définissent les éléments dans votre SVG, les données sont également lié (joint) pour les éléments de votre document. Un cercle n'est pas seulement un élément de cercle avec un x, y et un rayon; les données proviennent de l'entités avant tout. Cette caractéristique de D3 permet aux données de diriger votre visualisation, non seulement lors de la création, mais tout au long de son cycle de vie.

# Conseils

* La courbe d'apprentissage peut être assez raide. Restez positif
* Commencez simplement, ajouter de la complexité petit à petit
* Reportez vous sur la documentation, les tutoriels
* **Cannibaliser le code** où et quand vous pouvez. D3 dispose de très bons exemples et la plupart sont accessibled gratuitement. Quelque chose vous intéresse ? Regarder comment cela a été fait.

# Début du tutoriel!

A la fin de ce tutoriel, nous aurons construit notre première (ou énième) carte avec D3! Ce ne sera pas la carte la plus jolie que vous ayez jamais faite, mais j'espère qu'une fois que vous l'aurez fait, vous aurez un tremplin pour faire encore mieux, par la suite, avec D3.

## Etape 1: Web Pages

Si vous avez besoin d'aide pour les élément d'une page web, consultez ce [tutoriel sur les webmaps](http://maptimesea.github.io/2014/11/05/web-map-intro.html#let-s-get-started). A partir de là, nous allons suivre les étapes pour partir d'une page vierge et obtenir notre modèle *map.html*. 

A ce stade, vous devriez avoir un fichier *map.html* sur votre poste que vous pouvez ouvrir dans votre navigateur. Ouvrez ce fichier *map.html* dans votre navigateur. Le fichier ressemble à ceci:

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
Maintenant, vous pouvez insérer dans votre page la librairie D3. Nous allons utiliser une version hébergée au lieu de la copier localement. Nous allons également ajouter une bibliothèque pour travailler du TopoJson. Ajouter cela directement après la balise `<head>` de `map.html`.

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

Ces lignes de code indiquent à votre navigateur que la variable *svg* est un élément SVG sous la balise body. Si le SVG n'existe pas, D3 va le créer. Ajoutez cela *map.html* et rechargez la page. Faites un clic droit dans la partie supérieure gauche de la page. Sélectionnez *Inspectez l'élement*, et vous verrez que votre page contient un élément SVG vide.

### Définir votre projection

~~~javascript
  var projection = d3.geo.equirectangular()
        .scale(153) // scales your map
        .translate([width / 2, height / 2]); // centers in SVG
~~~

Lorsque l'on définit une projection, on indique à D3 comment transformer les données sphérique en coordonnées Cartésiennes. Regardez la [documentation sur les projections](https://github.com/mbostock/d3/wiki/Geo-Projections) pour choisir une projection des données avec D3. 

### Traduire en coordonnées d'écran

~~~javascript
  var path = d3.geo.path()
        .projection(projection);
~~~

Lorsque D3 déssine votre SVG, it traduit les coordonnées géographiques en coordonnées de pixels sur l'écran. Cette fonctionnalité est appelée *path generator*. Dans ce bloc, nous stockons le path dans la variable *path* afin d'y accéder tout au long de notre code.
A cette étape, nous avons un SVG vide sur notre page. Notre **document** est prêt, nous avons besoin des **données**.

## Etape 3: Ajouter les données

Les données que nous allons utilisées sont stockées au format TopoJSON. Nous n'en aborderons pas ici les spécifictés, mais si vous voulez en savoir plus sur ce format, regardez le [wiki](https://github.com/mbostock/topojson/wiki).

Les données nous attendent [ici](https://gist.github.com/abenrob/ab3de11f64071ddc4f68). _(cliquer "Download Gist")_

Télécharger les données et décompresser les ! Si vous ne avez pas de logiciel pour décompresser les données, vous pouvez également copier le contenu brut dans un fichier sur votre ordinateur et enregistrer en tant que *worldtopo.js*. Déplacer *worldtopo.js* dans le même répertoire que *map.html*

## Etape 4: Charger les données 

Si vous regardez le début du fichier *worldtopo.js*, vous allez voir

~~~javascript
var worldtopo = {...}
~~~

Nous avons progressé en définissant les données comme une variable javascript, ainsi nous aurons pas besoin d'un serveur web pour faire ce tutoriel. Pour utiliser ces données, nous avons besoin de charger le fichier comme un script. Juste au-dessous des deux autres bibliothèques, nous allons ajouter une référence à ce fichier. Cet partie doit maintenant ressembler à ceci:

~~~markup
  <script src="http://d3js.org/d3.v3.min.js"></script>
  <script src="http://d3js.org/topojson.v0.min.js"></script>
  <script src="worldtopo.js"></script>
~~~

Lorsque vous avez terminé cette étape, vous aurez une carte! En dessous de votre générateur de trajectoire, insérer ce qui suit:

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

Cette portion de code utilise *worldtopo.js* et ajoute deux entités (les frontières et la couche terrestre) au générateur de path. C'est le  moment pour *Inspecter l'élement* sur votre page web ou creuser dans le TopoJSON. Comme vous pouvez le voir, votre SVG possède un élément "path" (une forme) pour chaque entité dans votre TopoJSON. Vous voyez comment les données dirige ce document ?

Maintenant votre carte ressemble à cela:

![bw map](/assets/img/tutorials/bw_map.png)

## Etape 5: Make it pretty

### ajouter des styles basiques

ajouter ces lignes à votre section `<style></style>` pour donner une jolie couleur bleue au fond et une bordure à l'élement svg, et pour colorier aussi les pays et les frontières.

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

Voici en plus joli:

![color map](/assets/img/tutorials/color_map.png)

### Ajouter un graticule

Nous ne avons pas besoin d'un graticule, mais c'est une chose disponible dans D3, nous allons donc l'utiliser !

Insérer ce code de graticule au bas de votre section `<script></script>`

~~~javascript
  var graticule = d3.geo.graticule();

  svg.append("g")
    .selectAll("path"),
    .data(graticule.lines)
    .enter().append("path")
    .attr("d", path),
    .attr("class", "graticule");
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

## Etape 6: Challenges

1. **Changer la translation**. Essayer .translate([400,800])  
2. **Changer l' échellee**. Essayer .scale(700)
3. **Changer la projection**. Essayer [autre chose](https://github.com/mbostock/d3/wiki/Geo-Projections). (un joli globe : d3.geo.orthographic()!)
4. **Trouver les données**. Utiliser votre console Javascript pour accéder aux élements de données. Astuce: il s'agit d'une [sélection](https://github.com/mbostock/d3/wiki/Selections)
5. **Charger les données à partir d'un fichier** Essayer ce fichier [TopoJSON](https://gist.github.com/abenrob/787723ca91772591b47e) - au lieu de le définir avec `var worldjson =`, charger le en utilisant [d3.json](https://github.com/mbostock/d3/wiki/Requests#d3_json). Exemples [ici](http://bost.ocks.org/mike/map/)
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

- [exemple de carte simplee](http://bl.ocks.org/abenrob/27290f6dd43de81578af)
- [exemple de rotation de carte](http://bl.ocks.org/abenrob/2d7a99385e44db333da7)
- [exemple de chargement JSON](http://bl.ocks.org/abenrob/c4ac3d581a7b16ff5f2f)

## Fin.

Tutoriel inspiré FORTEMENT de [MaptimeSea](http://maptimesea.github.io/2015/01/07/d3-mapping.html) et [maori.geek](http://www.maori.geek.nz/post/d3_js_geo_fun). Regarder aussi le reste de leurs travaux !