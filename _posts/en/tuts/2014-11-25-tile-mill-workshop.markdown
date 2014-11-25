---
layout: tut
title: "Introduction to TileMill"
date: 2014-11-17 12:15:00
author: abenrob
lang: en
categories:
- en
---
With [TileMill](https://www.mapbox.com/tilemill/), you can make beautiful, interactive maps. We are going to be walking through a condensed version of the [MapBox](http://www.mapbox.com) crashcourse for TileMill, which is located [here](https://www.mapbox.com/tilemill/docs/crashcourse/introduction/) in it's original form. 



We are moving it here for this workshop, to facilitate an upcoming French language translation.

### From [Mapbox.com](https://www.mapbox.com/tilemill/docs/crashcourse/introduction/):

# Introduction

## Step-by-step instructions for making maps

This crash course walks through how to design custom web maps in TileMill. It will help you quickly get familiar with the basics of map design with TileMill, from importing and styling your data to designing and sharing your map. 

## What we'll cover

<div class='summary'>
	
  <p><strong>Importing data</strong><br /> We walk through how to download data and get it into TileMill. To work with more complex data and datasets, check out the <a href="https://www.mapbox.com/tilemill/docs/manual/adding-layers">Adding Layers documentation</a>.</p> 

  <p><strong>Styling data</strong><br /> Here we show the basics of using CartoCSS to style your data and make a truly custom map. For an a in-depth dive into how Carto works, check out the <a href="https://www.mapbox.com/tilemill/docs/manual/carto">Carto manual</a>.</p> 

  <p><strong>Adding tooltips and legends</strong><br /> Making your map interactive and adding a legend is critical to communicating your data. Here we show how to add tooltip interactivity and legends to maps. The <a href="https://www.mapbox.com/tilemill/docs/manual/project-settings">Project Settings</a> documentation offers more advanced details on this. </p> 

  <p><strong>Exporting your map</strong><br /> Maps made in TileMill can be shared quickly and easily in a number of formats. Here we show how to export an interactive map to the MBTiles format for uploading on the web. See our <a href="https://www.mapbox.com/tilemill/docs/manual/exporting/">Exporting docs</a> for an overview of all supported exporting formats.</p> 

</div>

# TileMill & GIS

TileMill is a desktop application for creating beautiful web maps. It was designed to easily integrate into existing GIS workflows to enhance design capabilities and flexibility. 

## Designing maps
Map design is the core of TileMill. To manipulate geospatial data to be used on TileMill maps, you may need to look outside of tool or integrate it with other GIS software. Some spatial data can be worked with in Google Docs and Microsoft Excel, and for others GIS software like [QuantumGIS](http://www.qgis.org/) or [ESRI ArcGIS](http://www.esri.com/software/arcgis/index.html) may be needed. Spatial database software like [PostGIS](http://postgis.refractions.net/) and [SQLite](http://sqlite.org) can also be used to work with large spatial datasets and integrated into TileMill.  

The main output of TileMill is tiled maps - millions of 256 px by 256 px images that can be loaded quickly in interactive maps - exported in MBTiles format. Tiled maps are the basic technology behind the best panning and zooming maps on the web. You can also export PNG and PDF files from TileMill for static output or use in presentations - using the same map styles that power your highly interactive maps.

## MBTiles
These map tiles are stored in a package file, called an [MBTiles file](http://mapbox.com/mbtiles-spec). This allows them to be compressed, copied, and transferred easily from place to place. Unlike most tiled maps, the maps you make in TileMill can be interactive - hovering and clicking on map tiles can trigger popups and even site navigation. The interactivity data is also compressed and stored in MBTiles files.

Unlike static maps, tiled maps tend to have many layers of detail - you’ll want to choose what features to show and hide at each zoom level. TileMill’s styling language CartoCSS makes this easy, and it's fast to learn how to use it with a built-in reference, autocomplete, and error highlighting - and even easier if you’re already comfortable with CSS.

# Point data

One of the [many geo formats](https://www.mapbox.com/tilemill/docs/manual/adding-layers/) that TileMill supports is a spreadsheet, specifically a [comma-separated values (CSV) file](http://en.wikipedia.org/wiki/Comma-separated_values). We want to start by working with a basic spreadsheet to show how easy it is to make a simple point map.  

## Your CSV spreadsheet
To import data into TileMill as a CSV file you need column headings on the first row. The CSV must also contain columns with latitude and longitude geographic coordinates. We have hard coded TileMill to look at the column headers for any mention of "lat" or "latitude", so something like "geo_longitude" will even work. 

If your CSV contains place names or addresses instead of lat/lon coordinates, you will have to geocode the data before it will work in TileMill. We have a [plugin for Google Docs](http://developmentseed.org/blog/2011/10/12/mapping-google-doc-spreadsheet/) that makes geocoding easy.

In this crash course, we'll use [earthquake data from the USGS](http://earthquake.usgs.gov/earthquakes/feed/) to make a map showing points for earthquakes that have occurred. 

1. Start TileMill and click on the "New project" button on the main screen.
  ![Add project](/assets/img/workshops/tilemill/csv-1.png)
2. Enter a "Filename" for your project and click "Add". You can leave the other fields alone for now.
  ![Add project](/assets/img/workshops/tilemill/csv-2.png)
3. Click on the new project to open it. The project contains a default layer called `#countries` styled with some example CartoCSS code.
4. To add a CSV layer, first click the "Layers" button located on the bottom left to bring up the Layers panel.
  ![Add layer](/assets/img/workshops/tilemill/csv-3.png)
5. Now click "Add layer".
  ![Add layer](/assets/img/workshops/tilemill/csv-4.png)
6. Enter `earthquakes` in the "ID" field. You'll use this ID to to reference this layer in CartoCSS selectors.
7. Enter the URL `http://earthquake.usgs.gov/earthquakes/feed/csv/2.5/week` into the "Datasource" field.
8. Click the "Save & Style" button. This will add the layer to your project and insert a default CartoCSS rule for the layer.
  ![Add layer](/assets/img/workshops/tilemill/csv-7.png)
9. Preview the result in the map preview pane.
  ![Styled map](/assets/img/workshops/tilemill/earthquake-map.png)

# Styling data

TileMill uses a language called CartoCSS to determine the look of a map. Colors, sizes, and shapes can all be manipulated by applying their relative CartoCS parameters in the stylesheet panel to the right of the map. Read the [CartoCSS manual](https://www.mapbox.com/tilemill/docs/manual/carto/) for a more detailed introduction to the language.

Previously, when we used the "Save & Style" button, the button automatically added several styling parameters to your stylesheet and gave them an initial value.
  ![Styling](/assets/img/workshops/tilemill/styling-1.png)

1. `#earthquakes`  
This is the layer to which the styles are applied.

2. `marker-width`  
This determines the size of the markers, in pixels. If `marker-height` is not specified, it is given the same value, resulting in a circle marker. You can experiment with changing this by typing in a different number.

3. Click the "Save" button to save your project and trigger the map preview to update.
  ![Save project](/assets/img/workshops/tilemill/save-project.png)

4. `marker-fill`  
This is the color of the inside of the marker. There are two methods for changing color values. You can either type in a new value, or you can use the color swatches at the bottom of the CartoCSS panel. Try changing the marker color by clicking the light red swatch and selecting a new color.
  ![Styling](/assets/img/workshops/tilemill/styling-3.png)

5. Click "Save" in the color picker to see your changes. Notice the corresponding color value is updated in the CartoCSS.
  ![](/assets/img/workshops/tilemill/color-picker-1.png)

6. `marker-line-color`  
This is the color of the border of the marker. This time try changing the color by replacing the hex value `#813` with `#000`, the code for black.

7. Click "Save". Notice that the color swatch is updated with the new color.

8. `marker-allow-overlap`  
This allows markers to be placed on top of each other. If this is not specified or set to `false`, potentially overlapping markers will not be displayed.

## Conditional styles

Conditional CartoCSS styles allow you to change the appearance of the points on your map based on attributes in the data. Here we will customize the earthquake points based on the magnitude of the quake.

1. Review the available data for the layer in the feature inspector.
  ![Feature inspector](/assets/img/workshops/tilemill/feature-inspector-1.png)
2. Find the column called `Magnitude` and examine the range of values. This will help you decide how to scale the points.
  ![Feature inspector](/assets/img/workshops/tilemill/feature-inspector-2.png)
3. Add the following CartoCSS rule to the bottom of your stylesheet. This CartoCSS rule sets a larger `marker-width` for features that have a larger `Magnitude` value.

        #earthquakes {
          [Magnitude >= 2.5] { marker-width:6; }
          [Magnitude >= 3]   { marker-width:8; }
          [Magnitude >= 3.5] { marker-width:10; }
          [Magnitude >= 4]   { marker-width:12; }
          [Magnitude >= 4.5] { marker-width:14; }
          [Magnitude >= 5]   { marker-width:16; }
          [Magnitude >= 5.5] { marker-width:18; }
          [Magnitude >= 6]   { marker-width:20; }
        }

4. Click "Save" to view your changes.
5. Use the map preview to confirm that the style is working. Adjust the CartoCSS rule until you are satisfied.
  ![Conditional style](/assets/img/workshops/tilemill/conditional-style-1.png)

These are just a few of the basic parameters for styling point data. For a comprehensive look at styling possibilities, checkout the [CartoCSS reference](/tilemill/docs/manual/carto/) section in TileMill and [online](http://mapbox.com/carto/).
  ![Styling](/assets/img/workshops/tilemill/styling-4.png)

# Tooltips & legends

Tooltips and legends allow you to add interactivity, additional information, and context to your maps. Below we'll walk through how to add each to your map.

## Tooltips

Tooltips allow you to make maps interactive with dynamic content that appears when a user hovers over or clicks on a map. They can contain HTML and are useful for revealing additional data, images, and other content.

1. Open the Templates panel by clicking on the pointer button on the bottom left.
  ![](/assets/img/workshops/tilemill/tooltips-6.png)
2. Click on the "Teaser" tab. Teaser content appears when you hover over a feature and Full content appears when you click on a feature. You can use the Location field to define a URL to be loaded when a feature is clicked.
  ![](/assets/img/workshops/tilemill/tooltips-1.png)
3. Select the "Earthquakes" layer to use it for interaction. TileMill only supports one interactive layer at a time.
  ![](/assets/img/workshops/tilemill/tooltips-2.png)
4. The data fields for the layer are displayed wrapped in curly [Mustache](http://mustache.github.com/) tags. These tags will be replaced by data when you interact with the map. Locate the fields you want to use.
  ![](/assets/img/workshops/tilemill/tooltips-3.png)
5. Write your template using the Mustache tags. Paste the following code into the Teaser field and use the preview to make sure it looks good:

    ```
    {% raw %}{{{mag}}} Magnitude Earthquake<br/>{{{place}}}{% endraw %}
    ```

6. Click "Save" to save your settings and refresh the map. Close the panel by clicking the close button (X) or by pressing the ESC key. Move your mouse over some points to see the tooltips.
  ![](/assets/img/workshops/tilemill/tooltips-5.png)

## Legends

A legend is permanently on a map and is useful for displaying titles, descriptions, and keys for what is being mapped. It can be styled using HTML, or it can simply contain an image.

Let's add a legend that describes the theme of the map.

1. Open the Templates panel by clicking on the pointer button in the bottom left.
  ![](/assets/img/workshops/tilemill/tooltips-6.png)
2. The Legend tab is open by default.
  ![](/assets/img/workshops/tilemill/legend-1.png)
3. Enter your legend text/html in the Legend field:

	```
    {% raw %}<strong>Magnitude 2.5+ Earthquakes (Past 7 Days)</strong><br/>Circle size indicates magnitude of earthquake.{% endraw %}
    ```

4. Click "Save" and close the panel. You will now see your legend in the bottom right corner of the map.
  ![](/assets/img/workshops/tilemill/legend-3.png)

<div class='note' markdown='1'>
<h3>Allowed HTML</h3>
For security, unsafe HTML in tooltips and legends are sanitized and JavaScript code is removed. If you want to build sophisticated map interaction with JavaScript on your own website, you can write custom code using the [MapBox.js API](https://www.mapbox.com/mapbox.js/api/v1.4.0/).
</div>

# Exporting

TileMill can export maps to MBTiles, PNG, PDF, SVG, or Mapnik XML formats. A full listing and overview is in the [Exporting documentation](https://www.mapbox.com/tilemill/docs/manual/exporting/).


1. Click the "Export" button. A drop down menu will appear.
  ![](/assets/img/workshops/tilemill/exporting-1.png)
2. Click "MBTiles". The window will transition to the export tool.
  ![](/assets/img/workshops/tilemill/exporting-2.png)
3. Choose a "Filename"". The name of the project will be placed here by default.
  ![](/assets/img/workshops/tilemill/exporting-3.png)
4. Select Zoom levels. Set the furthest zoom to 1 by dragging the left end to the right. Set the closest zoom to 6 by dragging the right end to the left.
  ![](/assets/img/workshops/tilemill/exporting-5.png)
The numbers below the slider update as the zoom level is moved. These numbers indicate how large the exported file will be. The wider the bounds and the more zoom levels in a map, the larger the final size of the MBTile file will be.
5. Select the "Center" of the map. This determines the starting center and zoom level of the map when it is first loaded. You can manually enter these values or click a point in the map preview. Zoom to level three and click the center of the United States.
  ![](/assets/img/workshops/tilemill/exporting-6.png)
6. Select the map "Bounds". This is the area of the map to be exported. By default the entire world is selected. If your map is allocated to a smaller region of the globe, you can save processing time and disk space by cropping to that area. This can be done by manually entering values in the Bounds fields, or by holding the SHIFT key and clicking and dragging on the map. Leave the default value.
  ![](/assets/img/workshops/tilemill/exporting-4.png)
6. Click "Export".
  ![](/assets/img/workshops/tilemill/exporting-7.png)
The window will transition back to your project and the exports panel will appear. Here is where you can access and find information about your exported files. The most recent will appear at the top of the list and display its progress as it is being processed.
  ![](/assets/img/workshops/tilemill/exporting-8.png)
7. When the export process is complete, the progress bar will be replaced by a Save button. This will save a copy of the file locally to a specified location.
  ![](/assets/img/workshops/tilemill/exporting-9.png)

## Great suff!

Here are a couple more steps to take, if you so desire:

* [Upload](https://www.mapbox.com/help/upload-mbtiles-file) your map to the web.
* [Sign up](https://www.mapbox.com/plans) for a Mapbox hosting account.
