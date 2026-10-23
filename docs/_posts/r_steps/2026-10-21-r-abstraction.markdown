---
layout: single
title:  "R Abstraction"
date:   2026-10-21 00:00:00 -0400
tags:
 - abstraction
---

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








