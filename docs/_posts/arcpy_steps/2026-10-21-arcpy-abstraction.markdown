---
layout: single
title:  "ArcPy Abstraction"
date:   2026-10-21 00:00:00 -0400
tags:
 - abstraction
---
## Abstraction 
The code above can be abstracted fairly easily into a function, shown below.

    ```py

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













