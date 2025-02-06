---
title: "Voronoi"

date: 2025-02-06T13:24:11-05:00
url: /Voronoi/
image: /images/2020-thumbs/Voronoi.jpg
categories:
  - Linux
  - Windows
  - Networking
tags:
  - Ubuntu
draft: true
---

The following is work from my undergrad apart of a geology class in 2023 using voroni polygons and other tools to look at air quality in California.
<!--more-->

## Lab 4: Using Voronoi Polygons for Air Quality Monitoring Network Design
2023-12-05
### Introduction

The objective of this lab is to introduce you to the concept of Voronoi polygons and demonstrate their practical application in designing an air quality monitoring network for California. You will learn how to create Voronoi polygons from ozone measurement data, identify suitable monitoring station locations, and explore the significance of such a network in air quality management. You will also compare this with spatial demographic data from the CDC/ATSDR Social Vulnerability Index.
### Libraries

Feel free to add any additional libraries you need
```r
library(tmap)
library(raster)
library(gstat)
library(sf)
library(tidyverse)
library(tigris)
library(plotly)
```

### Dr G’s Set-Up Code

You do not need to edit the code chunk below, It should run and be invisible when you knit. I did this because I couldn’t suppress the loading messages in any other way. Dr G: My code does several things

1.    I use the Tigris package to read in the census tract and county borders for California
2.   I then directly download the SOVI data from a government portal. If you put the url into an internet browser it will also directly download.
3.    The SOVI files are huge. This is a one week lab, so I tidied up
4.    I switched all the -999s to NA using the mutate command
5.    Then I joined the SOVI data with the spatial census tract/county borders - to make
      1.  tract.sovi.sf - the sovi data at census tract level
      2.  county.sovi.sf - the sovi data at county level
6.    Finally I changed the map projection to the North American Albers Equal Area Conic projection (AEA)

You do not need to edit this code chunk, It should run and be invisible when you knit. I did this because it kept messing up my output when I knit.

## Challenge 1. Comprehension

### Qu. 1A

Describe what census tracts are and why they are commonly used as a spatial unit.

Census tracts are areas where a census would take place. These are used as a spatial unit to draw statistical information from a population area

### Qu. 1B

In my code, I change the data to the North American Albers Equal Area Conic projection (AEA). Explain what this is and how it’s different to lat/long or UTM data.

**This is different from lat/long or UTM data as it is a different type of projection that uses two standard parallels to reduce distortions. Making this best used for land masses in the middle latitudes like the United States and Europe.**

### Qu. 1C

In my code, I use the command rm(list=ls()).
What does this do? Remember google/chatGPT in helping you answer this.

**This clears the environment data to give you a clean workspace as if you made a new project.**

### Qu. 1D

You will find the reference for SOVI here. https://www.atsdr.cdc.gov/placeandhealth/svi/data_documentation_download.html

Edit for CA, then write a sentence explaining where the data is from and add a footnote with your reference. To do this, click the visual button at the top, then go to Insert/Footnote. (HINT FOR PROJECTS)

**The data came from the CDC/ATSDR Social Vulnerability Index.**

### Qu. 1E

How did I make my set-up code chunk invisible when this file knits? Hint look at the code chunk options.

**Including “message=FALSE,warning=FALSE,echo=FALSE,include=FALSE” in the code chunk makes the chunk itsself invisible.**

## Challenge 2. SOVI

### Qu. 2A. SOVI Basics

Explain what SOVI is in your own words, using the lab instruction resources as a guide

**SOVI is an index that sees how people are able to withstand hazards or things beyond their control that negatively affect their life.**

### Qu. 2B: Themes

In the sovi dataset, you should see these columns (“RPL_THEME1” “RPL_THEME2” “RPL_THEME3” “RPL_THEME4” “RPL_THEMES”). They are described here: Sovi documentation and in the SOVI recipe in the lab book.

Describe what each of these columns represents. If you talked about this in above, you can refer to your answer.

**RPL_THEME1 - Percentile ranking for Socioeconomic Status theme summary RPL_THEME2 - Percentile ranking for Household Characteristics theme summary RPL_THEME3 - Percentile ranking for Racial and Ethnic Minority Status theme RPL_THEME4 - Percentile ranking for Housing Type/ Transportation theme RPL_THEMES - Overall percentile ranking**

### Qu. 2C: SOVI theme maps

YOU HAVE A CHOICE.

If you’re interested and your computer is powerful, use the census-tract sovi sf data at any time. If your computer struggles/you use the cloud or you want it to run fast, use the county level sovi sf data.

Create four professional maps showing the SOVI Themes 1-4 for California.


Tmap is a little different with polygon data, so here’s some example code also showing how to do subplots

-    When you have edited it and are ready, remove the # at the front of each line you want to run. Or you’re welcome to delve into the more complex tmap packages if you like https://bookdown.org/nicohahn/making_maps_with_r5/docs/tmap.html

-    For MANY color options, see https://www.nceas.ucsb.edu/sites/default/files/2020-04/colorPaletteCheatsheet.pdf


```r
#testdata is the name of my variable. Yours will be the census tract or county sovi sf data.  ColumnName is the name of the column you want to color
map1 <- qtm(county.sovi.sf,fill="RPL_THEME1",fill.palette="Greens") #Socioeconomic Status theme summary
map2 <- qtm(county.sovi.sf,fill="RPL_THEME3",fill.palette="Blues") #Racial and Ethnic Minority Status them
map3 <- qtm(county.sovi.sf,fill="EP_NOHSDP",fill.palette="Reds") #persons with no HS Diploma 
map4 <- qtm(county.sovi.sf,fill="EP_MUNIT",fill.palette="Purples") #Housing in buildings with 10 or more units 
tmap_arrange(map1,map2,map3,map4)

```


![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/RPL+More.png
?raw=true)

### Qu. 2D: Map interpretation

Tell me what patterns and processes you see in your maps. Referring to your maps, which areas of California do you think might struggle with the social impact of air pollution and why.

**The southern and middle parts of California show a lot of overlap with socioeconomic status and racial and ethnic minority status. While the persons with no HS diploma are also represented in this group being centered just south of the middle of California. When it comes to who would be the most likely to struggle with the social impact of air pollution it looks as though it would be these persons in these areas. Although, having a HS diploma doesn’t make a person inherently more susceptible to air pollution economic mobility is affected.**

### Qu. 2E: MAUP

Think about MAUP and gerrymandering. Explain why is it important to think hard about choosing between the county or census level SOVI data.

**Depending upon which is used a completely different story could be told. For instance, one could show low levels of something while one doesn’t have enough data to even show up on a graph.If you need a more detailed and localized understanding of social vulnerability, census-level data is likely the better choice. However, if you are looking at larger geographic trends or making high-level policy decisions, county-level data might be sufficient.**


### Challenge 3. Ozone data

### Qu. 3A. Ozone summary

We will now read in some data on Ozone. You can read about it in the lab book. Summarise what the data is showing.
Qu. 3B. Ozone read-in

Replace the XXXXXs with the correct values to get this to run. Your data should already be in your project file. Hint, not all the XXXXXs are the same!


```r
# point.ozone$LONGITUDE
# Read in Ozone Data
point.ozone     <- read.csv("CA_OzonePopulation.csv")
point.ozone.sf  <- st_as_sf(point.ozone, coords=c("LONGITUDE","LATITUDE"),crs=4326)

# Transform to projection 3310 - hint look at my set-up code chunk.
# I'm simply overwriting my sf data as my answer
point.ozone.sf <- st_transform(point.ozone.sf,3310)

```

### Qu. 3C. Ozone summary

Use R to summarise the ozone data, then answer these Qu.s in the text


```r
#summarise the ozone data
summarise(point.ozone.sf)
```


```r
## Simple feature collection with 1 feature and 0 fields
## Geometry type: MULTIPOINT
## Dimension:     XY
## Bounding box:  xmin: -353684.6 ymin: -624323.6 xmax: 501010.6 ymax: 432274.9
## Projected CRS: NAD83 / California Albers
##                         geometry
## 1 MULTIPOINT ((-353684.6 3143...
```

```r
#the max ozone point in ppb
max(point.ozone.sf$POPULATION_DENSITY)
```

```r
## [1] 406.6252
```

-    How many monitoring stations are there in the dataset? There are 451 monitoring stations

-    What is the Ozone at site name: Beverly Hills-Franklin? Remember units! 33.50973 ppb

-    What is the maximum population density? 406.6252

### Qu. 3D. Ozone vs Population density.

Using the plotly command, use this tutorial to make a map of ozone values vs population density at each location.

https://plotly.com/r/line-and-scatter/

https://psu-spatial.github.io/Geog364-2023/in_Tutorial08Plots.html#Interactive

Explain do you see - is it what you expect given the sources of ozone data?


```r
library(plotly)


fig <- plot_ly(data = point.ozone, x = ~POPULATION_DENSITY, y = ~OZONE_1000PPB,

  color = ~OZONE_1000PPB, size = ~POPULATION_DENSITY)


fig

```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/plotly.png?raw=true)



**Given the source of the data the ozone ppm seems smaller than what I thought it would be honestly. With the majoirty of the ozone present in a lower population density seems to be counter to what I would expect.**

### Qu. 3E. Ozone maps

Make two map showing the Ozone levels and the population density across California at the measuring sites. Hint (lab 3/reports etc). Bonus if you want to explore leaflet

```r
#plot(point.ozone$OZONE_1000PPB) placeholder just to copy the variables
#plot(point.ozone$POPULATION_DENSITY) placeholder just to copy the variables 


#map showing Ozone levels across California at the measuring sites
ozone_map <- ggplot() +
  geom_sf(data = point.ozone.sf, aes(color = OZONE_1000PPB), size = 3) +
  scale_color_gradient(low = "blue", high = "red", name = "Ozone Level") +
  labs(title = "Ozone Levels at Measuring Sites") +
  theme_minimal()
ozone_map

```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/airqual.png?raw=true)



```r
#map showing Population Density across California at the measuring sites
density_map <- ggplot() +
  geom_sf(data = point.ozone.sf, aes(color = POPULATION_DENSITY), size = 3) +
  scale_color_gradient(low = "lightblue", high = "darkblue", name = "Population Density") +
  labs(title = "Population Density at Measuring Sites") +
  theme_minimal()
density_map

```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/densitymap.png?raw=true)


Compare the maps visually. Is the ozone where you expect it to be? What might be happening? (hint, ozone is VERY light and CA is coastal)


```r
#package to combine the 2 mpas to be on 1 graph
#install.packages("gridExtra")
library(gridExtra)

combined_map <- grid.arrange(density_map, ozone_map, ncol = 2)

```

```r
# Display the combined map
print(combined_map)
```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/density+airq.png?raw=true)


```r
## TableGrob (1 x 2) "arrange": 2 grobs
##   z     cells    name           grob
## 1 1 (1-1,1-1) arrange gtable[layout]
## 2 2 (1-1,2-2) arrange gtable[layout]
```


### Conclusion
**The ozone is in areas that I would expect it to be as it covers the areas with less population density. In the areas with higher population density there are lower levels of ozone. Which may speak to the levels of people living in the area having an affect on the level of ozone in the atmosphere.**

1.2020 Database State. https://www.atsdr.cdc.gov/placeandhealth/svi/data_documentation_download.html. Accessed on 11/06/2023.↩︎
















