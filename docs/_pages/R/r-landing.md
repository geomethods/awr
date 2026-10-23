---
title: "R Workflow"
layout: single
permalink: /docs/r/
---

## Workflow Diagram
{% include figure popup=true image_path="/assets/images/R/r_awr_wf.png" alt="Alt text" caption="R Workflow" %} 

## Step #1: Setting up the Environment
Download the data required for this workflow, following the steps in the [Set Up]({{ '/setup/' | relative_url }}) tab. This workflow was developed on RStudio version 2026.01.2+418, and uses the sf and tidyverse libraries. Read in the data using the st_read() command, and reproject tracts into EPSG 3437 using the st_transform function 

    ```r
    #Set up the Environment
    library(sf)
    library(tidyverse)

    #Read in Senate Districts
    senate_districts <- st_read("New_Hampshire_Senate_District_Boundaries_-_2022.shp")

    #Read in and Reproject tracts
    nh_tracts_race<- st_read("nh_tracts_final.gpkg")
    tracts_reproj <- st_transform(nh_tracts_race,3437)
    ```

*Tip: if you see st_ in front of something in are, it comes from the sf library*

## Step #2: Calculating Source Polygon Area
Once the data has been read in and is in the  appropriate projection, we can calculate source polygon area. To add an field to a layer in R, use the mutate function. Put the field name, then an equals sign, followed by whatever you would like to calculate. In this case, we use st_area() 

    ```r
    tracts_reproj <- tracts_reproj %>% 
        mutate(sArea = st_area(tracts_reproj))
    ```

*Tip: if your area values are strange, check your projection using st_crs()*

## Step #3: Union 
The next step is to use st_intersection to generate fragments to re-aggregate. The function st_union does exist, but don't use it! It acts similarly to a singleparts to multiparts tool in a Geoprocessing GUI. 

    ```r    
    frags <- st_intersection(tracts_reproj,senate_districts)
    ```


## Step #4: Calculate Area Weighted Attributes
We can use the mutate function again to add fields to our layer. Calculate fragment area, the area weight, and the area-weighted attributes. Since st_area calculations includes units, wrap the aw calculation in as.numeric() to ensure your result only contains a number.

    ```r
    frags <- frags %>% mutate(
    fArea = st_area(frags),
    aw = as.numeric(fArea/sArea),
    aw_pop = aw * B02001_001E,
    aw_white = aw * B02001_002E,
    aw_black = aw * B02001_003E,
    aw_asian = aw * B02001_005E)
    ```
    
## Step #5: Re-aggregate 
The final step is to re-aggregate our geometry to districts. Use `Senate2022` in the group by function to instruct R you want to group your layer by an attribute. 

Next, use the summarise() function to aggregate the new fields we created and dissolve geometries. Setting na.rm = TRUE tells R to ignore NA values, which can be helpful to add if the values is aw_districts don't make sense. 

    ```r
    aw_districts <- frags %>%
        group_by(Senate2022) %>%
        summarise(
            # sum  numeric columns 
            population = sum(aw_pop, na.rm = TRUE),
            black = sum(aw_black, na.rm = TRUE),
            white = sum(aw_white, na.rm = TRUE),
            asian = sum(aw_asian, na.rm = TRUE)      
        )
    ```


## Step #6: Calculate Post-hoc attributes
When discussing gerrymandering through a geospatial lense, a helpful metric to calculate is `Compactness`. By comparing a given district's geometry to a circle, compactness provides a concrete way to see how irregular a given district is. 

The process used to calculate the builds off of functions we used to accomplish the AWR workflow. Use mutate, st_area, and st_perimeter to calculate the compactness of each district.

    ```r
    aw_districts <- aw_districts %>% 
        mutate(
            dArea = as.numeric(st_area(aw_districts)) ,
            dPerimeter = as.numeric(st_perimeter(aw_districts)) ,
            compactness = (400 * dArea * 3.1415926)/(dPerimeter^2)
        )
    ```


## Abstraction
When using scripting languages to accomplish workflows, it can be helpful to create functions to automatize actions we repeat often.

Here is one way this can be done in R for this specific example

    ```r
    awr <- function(tracts,senate){
  tracts_reproj <- st_transform(tracts,3437)
  #### Step #2: Source Polygon Area#####
  tracts_reproj <- tracts_reproj %>% 
    mutate(sArea = st_area(tracts_reproj))
  #### Step #3: Union#####
  frags <- st_intersection(tracts_reproj,senate)
  #### Step #4: Area weighted attributes #####
  aw_districts <- frags %>% 
    mutate(
      fArea = st_area(frags),
      aw = as.numeric(fArea/sArea),
      aw_pop = aw * B02001_001E,
      aw_white = aw * B02001_002E,
      aw_black = aw * B02001_003E,
      aw_asian = aw * B02001_005E
    ) %>% 
    group_by(Senate2022) %>%
    summarise(
      # sum  numeric columns 
      population = sum(aw_pop, na.rm = TRUE),
      black = sum(aw_black, na.rm = TRUE),
      white = sum(aw_white, na.rm = TRUE),
      asian = sum(aw_asian, na.rm = TRUE)

    )
  return(aw_districts)
}

aw_districts <- awr(nh_tracts_race,senate_districts)
    ```








