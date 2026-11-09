---
layout: single
title: "Area Weighted Re-Aggregation"
permalink: /
---


Hello Geographers! For more information on how to use this site, please read the [Help Page]({{ '/help/' | relative_url }})
and consider reading the [Conceptual Guide]({{ '/guide/' | relative_url }})

## Workflow Overview and Findings 
This workflow evaluates the extent State Senate Districts in New Hampshire are racially gerrymandered. NH, a perennial battleground state, becomes the source of much ire every two years when every seat in its legislature goes up for re-election. Both chambers of its bicameral legislature have flipped between Democratic and Republican control several over the last few decade, making every election and vote particularly important. Given this importance, Parties are eager to claim any advantage they can get through gerrymandering and other means. In the most recent redistricting cycle,  Republican control of both chambers has led to a map that likely favors that party; we evaluate the extent that it disadvantages racial minorities in the state.

Compactness is used to identify gerrymandered districts. By comparing how closely a district resembles a circle, one is able to quantify irregularly shape districts that could have been drawn to benefit a political party or disadvantage  
{% include figure popup=true image_path="/assets/images/findings/compactness2.png" alt="Alt text" caption="Figure #1: Compactness in 2022 State Senate Districts" %} 

Although New Hampshire geography leads to lower compactness in the north of the state, this is not the case in the south. For example, District 9 stretches from the center of the state to its western border: a particularly egregious example of the gerrymandering. Other districts in the top two quantiles are also particularly serpentine, ducking and diving to capture towns the majority party hopes will result in their candidate winning. Though Figure #1 clearly demonstrates gerrymandering in the state, it does not show if it was racially motivated.

{% include figure popup=true image_path="/assets/images/findings/pctPOC1.png" alt="Alt text" caption="redo this figure" %} 

Figure #2 shows percent POC in NH Senate districts below median compactness. Although their is some overlay between districts with a high percentage of people of color and low compactness, NH does not have a particularly large number of POC, making in unclear if these districts were drawn to disadvantage minorities in the state.


{% include figure popup=true image_path="/assets/images/findings/pctwhite.png" alt="Alt text" caption="Percent White" %} 

Figure #3, showing the percentage of the population that is white, supports this conclusion; although some gerrymandered districts have a lower percentage of white residents, it is far from a universal trend, making it difficult to come to any hard conclusions with this data. 

There is extensive gerrymandering in the NH State Senate. With this data, it is difficult to come to a meaningful conclusion as to whether or not it was done on the basis of race. However, if one were to obtain the political party registration or election results, one could likely make an argument of partisan gerrymandering. Using one of the workflows introduced in this crosswalk, we encourage you to find this data (likely on a precinct level) and perform AWR to answer this question. Best of luck!











