---
title: "QGIS Workflow"
layout: single
permalink: /docs/qgis/
---

## Workflow Diagram
{% include figure popup=true image_path="/assets/images/awr_wf_qgis.png" alt="Alt text" caption="QGIS Workflow" %} 

## Step #1: Setting up the Environment

Download the data required for this workflow, following the steps in the [Set Up]({{ '/setup/' | relative_url }}) tab. Fire up QGIS, and open up a new QGIS Project; This workflow was developed on Version 3.44.14-Solothurn. Drag the Senate and Tract boundaries in from the browser. 

### Downloading MiMiGIS

This workflow employs the MiMiGIS plugin for its **Group By** function, as it provides a more effective way for aggregating and dissolving geometries than vanilla QGIS.

{% include figure popup=true image_path="/assets/images/qgis/plugin_nav.png" alt="Alt text" caption="" %} 
To install this plugin, first navigate to the Plugins tab on the masthead. 

{% include figure popup=true image_path="/assets/images/qgis/mimi_nav.png" alt="Alt text" caption="" %} 
Enter MiMiGIS into the search bar, and press install. Once installed, you can verify the plugin was downloaded by searching for **Group By** in the Processing Toolbar.
{% include figure popup=true image_path="/assets/images/qgis/step_1_reproj.png
" alt="Alt text" caption="" %} 
We also need to Reproject the Census Tracts to CRS: EPSG:3437-NAD83 in order to ensure accurate area calculations. 

Here is what the QGIS window should look like after this step
{% include figure popup=true image_path="/assets/images/qgis/q_1_env.png" alt="Alt text" caption="" %} 

## Step #2: Calculating Source Polygon Area

In this workflow, the demographic information we desire for our analysis is located within Census Tracts, and our desired geometry is the State Senate Districts. Therefore, Census Tracts are our `Source Polygons`.

In order to calculate area, left click on the tracts layer to open up the Attribute table, followed by the Field Calculator (the abacus symbol).
{% include figure popup=true image_path="/assets/images/qgis/step_2_field.png
" alt="Alt text" caption="" %} 
Enter sArea as the name, Decimal as the type, and area($geometry) as the expression. Press Ok or Apply to add the field to the attribute table. The formula area($geometry) calculates the planar area of each tract. 

## Step #3: Union 

The next step is use the Vector Overlay tool **Union**. 
{% include figure popup=true image_path="/assets/images/qgis/union_q.png
" alt="Alt text" caption="" %} 
Here is what your Layers Panel should look like following the Union.
{% include figure popup=true image_path="/assets/images/qgis/post_union_env.png
" alt="Alt text" caption="" %} 

## Step #4: Calculate Area Weighted Attributes
Once you have obtained the fragments of the Senate Districts and Tracts, calculate their area.
{% include figure popup=true image_path="/assets/images/qgis/fArea_q.png
" alt="Alt text" caption="" %} 
Then, divide fragment area by the area of the `Source Polygons`. This gives us the amount of space each fragment takes up in terms of source polygon. This is the Area Weight.
{% include figure popup=true image_path="/assets/images/qgis/aw_calc_q.png
" alt="Alt text" caption="" %} 
Multiply the Area Weight by your chosen attributes. Assuming population is distributed equally across each tract, this gives you the amount of people in each fragment.
{% include figure popup=true image_path="/assets/images/qgis/aw_ex_q.png
" alt="Alt text" caption="" %}  
## Step #5: Re-aggregate 
After calculating the area weighted attributes, the final step is to re-aggregate the fragments in terms of the Senate districts. Using the unique identifier for the Senate districts, use the MiMiGIS Group By Function to regain the original senate geometry 
{% include figure popup=true image_path="/assets/images/qgis/q_groupby.png
" alt="Alt text" caption="" %}

## Step #6: Calculate Post-hoc attributes

When discussing gerrymandering through a geospatial lense, a helpful metric to calculate is `Compactness`. By comparing a given district's geometry to a circle, compactness provides a concrete way to see how irregular a given district is. 

Calculate the district area and perimeter, and then use the formula below to calculate compactness.
{% include figure popup=true image_path="/assets/images/qgis/compactness_q.png
" alt="Alt text" caption="" %}





