---
title: "ArcGIS Pro Workflow"
layout: single
permalink: /docs/esri/arcgis/
---
## Step #1: Setting up the Environment
{% include figure popup=true image_path="/assets/images/arcgis/arc_reproj_awr.png
" alt="Alt text" caption="" %} 



## Step #2: Calculating Source Polygon Area


{% include figure popup=true image_path="/assets/images/arcgis/arc_sArea_awr.png
" alt="Alt text" caption="" %} 


## Step #3: Union 

The next step is use the Vector Overlay tool **Union**. 
{% include figure popup=true image_path="/assets/images/arcgis/arc_union_awr.png
" alt="Alt text" caption="" %} 


## Step #4: Calculate Area Weighted Attributes
Once you have obtained the fragments of the Senate Districts and Tracts, calculate their area.
{% include figure popup=true image_path="/assets/images/arcgis/arc_fArea_awr.png
" alt="Alt text" caption="" %} 
Then, divide fragment area by the area of the `Source Polygons`. This gives us the amount of space each fragment takes up in terms of source polygon. This is the Area Weight.
{% include figure popup=true image_path="/assets/images/arcgis/arc_attr_awr.png
" alt="Alt text" caption="" %} 
Multiply the Area Weight by your chosen attributes. Assuming population is distributed equally across each tract, this gives you the amount of people in each fragment.

## Step #5: Re-aggregate 

{% include figure popup=true image_path="/assets/images/arcgis/arc_diss_awr.png
" alt="Alt text" caption="" %}

## Step #6: Calculate Post-hoc attributes
{% include figure popup=true image_path="/assets/images/arcgis/arc_perim_awr.png
" alt="Alt text" caption="" %}

{% include figure popup=true image_path="/assets/images/arcgis/arc_comp_awr.png
" alt="Alt text" caption="" %}

## Step #7: Abstraction

ESRI allows you to abstract geoprocessing workflows into functional models using `Model Builder`. Although we will not go into detail on how to use this tool here, an example of how this workflow would look like in model builder has been provided below

{% include figure popup=true image_path="/assets/images/arcgis/arc_mb_awr.png
" alt="Alt text" caption="" %}





