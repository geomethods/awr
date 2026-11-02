---
title: "ArcPy Workflow"
layout: single
permalink: /docs/esri/arcpy/
---
## Step #1: Setting up the Environment
This workflow was developed on ArcGIS Pro 3.7. Download the data required for this workflow, following the steps in the [Set Up]({{ '/setup/' | relative_url }}) tab. Load your data into the environment. 

The Code for this workflow was adapted from the model builder shown at the end of the [ArcGIS]({{ '/docs/esri/arcgis' | relative_url }}) tab. Resources are linked as appropriate for alternate methods if one is not working off of an exported model builder.

First,set up your workspace in a python notebook on ArcGIS Pro. Set overwrite output to true to allow for more efficient variable naming. 

    ```py

    arcpy.env.workspace = r"path"
    arcpy.env.overwriteOutput = True

    ```

Next, Reproject tracts into EPSG: 3437 to ensure accurate area calculations. See this reference [](https://pro.arcgis.com/en/pro-app/3.6/tool-reference/data-management/project.html) to exclude some of the excess text in the function. 

*Tip: When the object in in_dataset, in_features, out_dataset, or out_feature_class is in quotes, it is a feature layer.*


    ```py 
    
    arcpy.management.Project(in_dataset="tracts", out_dataset="tracts_reproj", out_coor_system="PROJCRS["NAD_1983_StatePlane_New_Hampshire_FIPS_2800_Feet",BASEGEOGCRS["GCS_North_American_1983",DATUM["D_North_American_1983",ELLIPSOID["GRS_1980",6378137.0,298.257222101]],PRIMEM["Greenwich",0.0],CS[ellipsoidal,2],AXIS["Latitude (lat)",north,ORDER[1]],AXIS["Longitude (lon)",east,ORDER[2]],ANGLEUNIT["Degree",0.0174532925199433]],CONVERSION["Transverse_Mercator",METHOD["Transverse_Mercator"],PARAMETER["False_Easting",984250.0,LENGTHUNIT["Foot_US",0.3048006096012192]],PARAMETER["False_Northing",0.0,LENGTHUNIT["Foot_US",0.3048006096012192]],PARAMETER["Central_Meridian",-71.66666666666667],PARAMETER["Scale_Factor",0.9999666666666667],PARAMETER["Latitude_Of_Origin",42.5]],CS[Cartesian,2],AXIS["Easting (X)",east,ORDER[1]],AXIS["Northing (Y)",north,ORDER[2]],LENGTHUNIT["Foot_US",0.3048006096012192],ID["EPSG","3437"]]")

    ```

## Step #2: Calculating Source Polygon Area
In this workflow, the demographic information we desire for our analysis is located within Census Tracts, and our desired geometry is the State Senate Districts. Therefore, Census Tracts are our `Source Polygons`.

Use the **Calculate Geometry Attributes** function to calculate the planar area for the source polygons. 


    ```py

    tracts_reproj = arcpy.management.CalculateGeometryAttributes(in_features="tracts_reproj", geometry_property=[["sArea", "AREA"]])[0] 
    ```

## Step #3: Union 

The next step is use the Vector Overlay tool **Union**. This gives you an attribute table with the fields and geometries of both layers.

    ```py
    arcpy.analysis.Union(in_features=[["tracts_reproj", ""], ["senate_districts", ""]], out_feature_class="frags") 

    ```

## Step #4: Calculate Area Weighted Attributes
Once you have obtained the fragments of the Senate Districts and Tracts, calculate their area using **Calculate Geometry Attributes**. 

    ```py

    frags = arcpy.management.CalculateGeometryAttributes(in_features="frags", geometry_property=[["fArea", "AREA"]])[0]

    ```

Then, divide fragment area by the area of the `Source Polygons`. This gives us the amount of space each fragment takes up in terms of source polygon. This is the Area Weight. Multiply the Area Weight by your chosen attributes, using the **Calculate Fields (multiple)** function for the greatest efficiency. Assuming population is distributed equally across each tract, this gives you the amount of people in each fragment.

    ```py
    
    frags = arcpy.management.CalculateFields(in_table=frags, expression_type="PYTHON3", fields=[["aw", "!fArea!/!sArea!", "", "DOUBLE"], ["aw_pop", "!aw!!B02001_001E!", "", "DOUBLE"], ["aw_white", "!aw!!B02001_002E!", "", "DOUBLE"], ["aw_black", "!aw!!B02001_003E!", "", "DOUBLE"], ["aw_asian", "!aw!!B02001_005E!", "", "DOUBLE"]])[0]

    ```

## Step #5: Re-aggregate 
Use the **Dissolve** function to re-aggregate the fragments to districts. Use Senate2022 as the Dissolve field, and enter the area weighted attributes you calculated into the Statistics Fields. Calculate sum for each attribute to obtain totals for the districts. 

    ```py

    arcpy.management.Dissolve(in_features=frags, out_feature_class="aw_districts", dissolve_field=["Senate2022"], statistics_fields=[["aw_pop", "SUM"], ["aw_white", "SUM"], ["aw_black", "SUM"], ["aw_asian", "SUM"]])

    ```

## Step #6: Calculate Post-Hoc Attributes
When discussing gerrymandering through a geospatial lense, a helpful metric to calculate is `Compactness`. By comparing a given district's geometry to a circle, compactness provides a concrete way to see how irregular a given district is. 

First, use **Calculate Geometry Attributes**. to calculate area and perimeter for the senate districts

    ```py
        aw_districts = arcpy.management.CalculateGeometryAttributes(in_features="aw_districts", geometry_property=[["dArea", "AREA"], ["dPerim", "PERIMETER_LENGTH"]])[0]
    ```

Next, use the following formula in **Calculate Fields** to obtain compactness for each district.

    ```py
        aw_districts = arcpy.management.CalculateField(in_table="aw_districts", field="compactness", expression="(400* 3.1415* !dArea!)/!dPerim!**2", field_type="DOUBLE")[0]
    ```
        
   
## Abstraction 
The code above can be abstracted fairly easily into a function, shown below.

    ```py

    awr("nh_tracts_race","New_Hampshire_Senate_District_Boundaries_-_2022")
    def awr(tracts, senate_districts):
        arcpy.env.overwriteOutput = True

        arcpy.management.Project(in_dataset=tracts, out_dataset="tracts_reproj", out_coor_system="PROJCRS["NAD_1983_StatePlane_New_Hampshire_FIPS_2800_Feet",BASEGEOGCRS["GCS_North_American_1983",DATUM["D_North_American_1983",ELLIPSOID["GRS_1980",6378137.0,298.257222101]],PRIMEM["Greenwich",0.0],CS[ellipsoidal,2],AXIS["Latitude (lat)",north,ORDER[1]],AXIS["Longitude (lon)",east,ORDER[2]],ANGLEUNIT["Degree",0.0174532925199433]],CONVERSION["Transverse_Mercator",METHOD["Transverse_Mercator"],PARAMETER["False_Easting",984250.0,LENGTHUNIT["Foot_US",0.3048006096012192]],PARAMETER["False_Northing",0.0,LENGTHUNIT["Foot_US",0.3048006096012192]],PARAMETER["Central_Meridian",-71.66666666666667],PARAMETER["Scale_Factor",0.9999666666666667],PARAMETER["Latitude_Of_Origin",42.5]],CS[Cartesian,2],AXIS["Easting (X)",east,ORDER[1]],AXIS["Northing (Y)",north,ORDER[2]],LENGTHUNIT["Foot_US",0.3048006096012192],ID["EPSG","3437"]]")

        tracts_reproj = arcpy.management.CalculateGeometryAttributes(in_features="tracts_reproj", geometry_property=[["sArea", "AREA"]])[0]
        
        arcpy.analysis.Union(in_features=[["tracts_reproj", ""], [senate_districts, ""]], out_feature_class="frags") 

        frags = arcpy.management.CalculateGeometryAttributes(in_features="frags", geometry_property=[["fArea", "AREA"]])[0]

        frags = arcpy.management.CalculateFields(in_table=frags, expression_type="PYTHON3", fields=[["aw", "!fArea!/!sArea!", "", "DOUBLE"], ["aw_pop", "!aw!!B02001_001E!", "", "DOUBLE"], ["aw_white", "!aw!!B02001_002E!", "", "DOUBLE"], ["aw_black", "!aw!!B02001_003E!", "", "DOUBLE"], ["aw_asian", "!aw!!B02001_005E!", "", "DOUBLE"]])[0]
        
        arcpy.management.Dissolve(in_features=frags, out_feature_class="aw_districts", dissolve_field=["Senate2022"], statistics_fields=[["aw_pop", "SUM"], ["aw_white", "SUM"], ["aw_black", "SUM"], ["aw_asian", "SUM"]])

    awr("nh_tracts_race","New_Hampshire_Senate_District_Boundaries_-_2022")

    ```



