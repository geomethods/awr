---
title: "R Workflow"
layout: single
permalink: /docs/R/
---

## Workflow Diagram


## Step #1: Setting up the Environment
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

## Step #2: Calculating Source Polygon Area
    ```r
    tracts_reproj <- tracts_reproj %>% 
        mutate(sArea = st_area(tracts_reproj))
    ```


## Step #3: Union 
    ```r
    frags <- st_intersection(tracts_reproj,senate_districts)

    ```


## Step #4: Calculate Area Weighted Attributes
    ```r
    frags <- frags %>% 
  mutate(
    fArea = st_area(frags),
    aw = as.numeric(fArea/sArea),
    aw_pop = aw * B02001_001E,
    aw_white = aw * B02001_002E,
    aw_black = aw * B02001_003E,
    aw_asian = aw * B02001_005E
  )
    ```
We can use the mutate function again to add attributes to our layer
## Step #5: Re-aggregate 
    ```r
    aw_districts <- frags %>%
        group_by(Senate2022) %>%
        summarise(
            # sum  numeric columns 
            population = sum(aw_pop, na.rm = TRUE),
            black = sum(aw_black, na.rm = TRUE),
            white = sum(aw_white, na.rm = TRUE),
            asian = sum(aw_asian, na.rm = TRUE),
            # combine geometries into one polygon per CZ
            geometry = st_union(geometry),
            .groups = "drop"
        )
    ```

## Step #6: Calculate Post-hoc attributes
    ```r
    aw_districts <- aw_districts %>% 
        mutate(
            dArea = as.numeric(st_area(aw_districts)) ,
            dPerimeter = as.numeric(st_perimeter(aw_districts)) ,
            compactness = (400 * dArea * 3.1415926)/(dPerimeter^2)
        )
    ```
## Abstraction
    ```r
    ```




