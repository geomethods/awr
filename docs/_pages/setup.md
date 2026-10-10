---
title: "Workflow #1 Context and Set Up"
permalink: /setup/
layout: single
---

## Context

Workflow #1 covers Area Weighted Re-Aggregation(AWR)(aka Areal Interpolation) of New Hampshire Senate Districts and Census Tracts. Like many states across the Country, NH has significant partisan gerrymandering. However, the impacts of this aren't clear without an examination of demographics of impacted areas. Since the NH Senate Districts do not have this data attached to them, we have to glean it from another data source using AWR (in this case Census Tracts). With this information we can answer questions like:

What are the demographic characteristics of gerrymandered districts?

Does gerrymandering in NH disadvantage any particular populations? 

And many more!
## Set up 

### Getting Senate Districts 

Follow this [link](https://new-hampshire-geodata-portal-1-nhgranit.hub.arcgis.com/datasets/new-hampshire-senate-district-boundaries-2022/explore?location=44.000000%2C-71.581250%2C8) and download the 2022 senate districts. Feel free to look at the maps provided to get an idea of what the data looks like. See [Workflow #1 Metadata](/workflow-1/metadata/) for more information on this data source. 

### Getting Census Tracts

To get Census data, one of the most efficient ways is using tidycensus in R. See the script below to download NH Census Tracts. Again,see [Workflow #1 Metadata](/workflow-1/metadata/) for more information on this data source

  ```r
    install.packages(c("sf","tidycensus","tidyverse"))
    library(sf)
    library(tidycensus)
    library(tidyverse)
    nh_tracts <- get_acs(
      geography = "tract",
      year = 2020,
      survey = "acs5",
      table = "B05003",
      state = "NH",
      output = "wide",
      geometry = TRUE
    )

    st_write(nh_tracts,"nh_tracts.gpkg")
  ```