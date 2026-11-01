---
title: "ArcGIS Pro Workflow"
layout: single
permalink: /docs/esri/arcgis/
---
## Step #1: Setting up the Environment
This workflow was developed on ArcGIS Pro 3.7. Download the data required for this workflow, following the steps in the [Set Up]({{ '/setup/' | relative_url }}) tab. 

Next, Reproject tracts into EPSG: 3437 to ensure accurate area calculations. 
{% include figure popup=true image_path="/assets/images/arcgis/arc_reproj_awr.png
" alt="Alt text" caption="" %} 



## Step #2: Calculating Source Polygon Area

In this workflow, the demographic information we desire for our analysis is located within Census Tracts, and our desired geometry is the State Senate Districts. Therefore, Census Tracts are our `Source Polygons`.

Use the **Calculate Geometry Attributes** tool to calculate the planar area for the source polygons. 
{% include figure popup=true image_path="/assets/images/arcgis/arc_sArea_awr.png
" alt="Alt text" caption="" %} 

*Tip: One could also use the Calculate Field tool for this step, but Calculate Geometry Attributes is more efficient*

## Step #3: Union 

The next step is use the Vector Overlay tool **Union**. This gives you an attribute table with the fields and geometries of both layers.
{% include figure popup=true image_path="/assets/images/arcgis/arc_union_awr.png
" alt="Alt text" caption="" %} 


## Step #4: Calculate Area Weighted Attributes
Once you have obtained the fragments of the Senate Districts and Tracts, calculate their area using **Calculate Geometry Attributes**. 
{% include figure popup=true image_path="/assets/images/arcgis/arc_fArea_awr.png
" alt="Alt text" caption="" %} 
Then, divide fragment area by the area of the `Source Polygons`. This gives us the amount of space each fragment takes up in terms of source polygon. This is the Area Weight.
{% include figure popup=true image_path="/assets/images/arcgis/arc_attr_awr.png
" alt="Alt text" caption="" %} 
Multiply the Area Weight by your chosen attributes, using the **Calculate Fields (multiple)** tool for the greatest efficiency. Assuming population is distributed equally across each tract, this gives you the amount of people in each fragment.

## Step #5: Re-aggregate 
Use the **Dissolve** tool to re-aggregate the fragments to districts. Use Senate2022 as the Dissolve field, and enter the area weighted attributes you calculated into the Statistics Fields. Calculate sum for each attribute to obtain totals for the districts.  
{% include figure popup=true image_path="/assets/images/arcgis/arc_diss_awr.png
" alt="Alt text" caption="" %}

## Step #6: Calculate Post-hoc attributes

When discussing gerrymandering through a geospatial lense, a helpful metric to calculate is `Compactness`. By comparing a given district's geometry to a circle, compactness provides a concrete way to see how irregular a given district is. 

First, use **Calculate Geometry Attributes**. to calculate area and perimeter for the senate districts
{% include figure popup=true image_path="/assets/images/arcgis/arc_perim_awr.png
" alt="Alt text" caption="" %}

Next, use the following formula in **Calculate Fields** to obtain compactness for each district.
{% include figure popup=true image_path="/assets/images/arcgis/arc_comp_awr.png
" alt="Alt text" caption="" %}

## Step #7: Abstraction

ESRI allows you to abstract geoprocessing workflows into functional models using `Model Builder`. Although we will not go into detail on how to use this tool here, an example of how this workflow would look like in model builder has been provided below

{% include figure popup=true image_path="/assets/images/arcgis/arc_mb_awr.png
" alt="Alt text" caption="" %}





