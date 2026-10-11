---
title: "Workflow #1 Metadata"
permalink: docs/metadata/
layout: single
---

## Item 1

- `Title`: NH State Senate Boundaries 
- `Abstract`: State Senate Boundaries in NH without demographic information
- `Spatial Coverage`: State of New Hampshire
- `Spatial Resolution`: 2022 State Senate Districts
- `Spatial Representation Type`: Vector Polygon
- `Spatial Reference System`: Specify the geographic or projected coordinate system for the study
- `Temporal Coverage`: 2022
- `Temporal Resolution`: 2022-2032
- `Lineage`: Downloaded from the NH State GeoData portal [here](https://new-hampshire-geodata-portal-1-nhgranit.hub.arcgis.com/datasets/new-hampshire-senate-district-boundaries-2022/explore?location=44.000000%2C-71.581250%2C8)
- `Distribution`: Available through the state of NH geodata portal
- `Constraints`:  Not for legal use

## Item 2

- `Title`: NH Tract Boundaries
- `Abstract`: Census Tracts in New Hampshire with demographic information. They do not nest neatly within State Senate Boundaries
- `Spatial Coverage`: State of New Hampshire
- `Spatial Resolution`: Census Tracts
- `Spatial Representation Type`: Vector Polygon
- `Spatial Reference System`: Specify the geographic or projected coordinate system for the study
- `Temporal Coverage`: 2016-2020
- `Temporal Resolution`: 2020
- `Lineage`: Downloaded from RStudio using tidycensus 1.8.1, using the following query:
    ```r
    nh_tracts <- get_acs(
      geography = "tract",
      year = 2020,
      survey = "acs5",
      table = "B05003",
      state = "NH",
      output = "wide",
      geometry = TRUE
    )
    ```
- `Distribution`: Available through the US Census Bureau website or Census API via RStudio and Tidycensus. 
- `Constraints`:  This product uses the Census Bureau Data API but is not endorsed or certified by the Census Bureau.