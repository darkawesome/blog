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

The following is work from my undergrad geology class in 2023. Which used Voronoi polygons and other tools to look at air quality in California.
<!--more-->

Although this work doesn't relect my current knowledge it is a great example of some of the work I have done in the past. This also exists as a repository of informaiton to look back at if similar work were to be done in the future. I have left in the instructions from the class as well. The majority of my work here is interpretation with a few parts being minor adjustments to the instructional code.

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

1. I use the Tigris package to read in the census tract and county borders for California
2. I then directly download the SOVI data from a government portal. If you put the url into an internet browser it will also directly download.
3. The SOVI files are huge. This is a one week lab, so I tidied up
4. I switched all the -999s to NA using the mutate command
5. Then I joined the SOVI data with the spatial census tract/county borders - to make
      1. tract.sovi.sf - the sovi data at census tract level
      2. county.sovi.sf - the sovi data at county level
6. Finally I changed the map projection to the North American Albers Equal Area Conic projection (AEA)

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

**Including “message=FALSE,warning=FALSE,echo=FALSE,include=FALSE” in the code chunk makes the chunk itself invisible.**

## Challenge 2. SOVI

### Qu. 2A. SOVI Basics

Explain what SOVI is in your own words, using the lab instruction resources as a guide

**SOVI is an index that sees how people can withstand hazards or things beyond their control that negatively affect their lives.**

### Qu. 2B: Themes

In the sovi dataset, you should see these columns (“RPL_THEME1” “RPL_THEME2” “RPL_THEME3” “RPL_THEME4” “RPL_THEMES”). They are described here: Sovi documentation and in the SOVI recipe in the lab book.

Describe what each of these columns represents. If you talked about this in above, you can refer to your answer.

**RPL_THEME1 - Percentile ranking for Socioeconomic Status theme summary RPL_THEME2 - Percentile ranking for Household Characteristics theme summary RPL_THEME3 - Percentile ranking for Racial and Ethnic Minority Status theme RPL_THEME4 - Percentile ranking for Housing Type/ Transportation theme RPL_THEMES - Overall percentile ranking**

### Qu. 2C: SOVI theme maps

YOU HAVE A CHOICE.

If you’re interested and your computer is powerful, use the census-tract sovi sf data at any time. If your computer struggles/you use the cloud or you want it to run fast, use the county level sovi sf data.

Create four professional maps showing the SOVI Themes 1-4 for California.

Tmap is a little different with polygon data, so here’s some example code also showing how to do subplots

- When you have edited it and are ready, remove the # at the front of each line you want to run. Or you’re welcome to delve into the more complex tmap packages if you like https://bookdown.org/nicohahn/making_maps_with_r5/docs/tmap.html

- For MANY color options, see https://www.nceas.ucsb.edu/sites/default/files/2020-04/colorPaletteCheatsheet.pdf

```r
#testdata is the name of my variable. Yours will be the census tract or county sovi sf data.  ColumnName is the name of the column you want to color
map1 <- qtm(county.sovi.sf,fill="RPL_THEME1",fill.palette="Greens") #Socioeconomic Status theme summary
map2 <- qtm(county.sovi.sf,fill="RPL_THEME3",fill.palette="Blues") #Racial and Ethnic Minority Status them
map3 <- qtm(county.sovi.sf,fill="EP_NOHSDP",fill.palette="Reds") #persons with no HS Diploma 
map4 <- qtm(county.sovi.sf,fill="EP_MUNIT",fill.palette="Purples") #Housing in buildings with 10 or more units 
tmap_arrange(map1,map2,map3,map4)

```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/RPL+More.png?raw=true)

### Qu. 2D: Map interpretation

Tell me what patterns and processes you see in your maps. Referring to your maps, which areas of California do you think might struggle with the social impact of air pollution and why?

**The southern and middle parts of California show a lot of overlap with socioeconomic status and racial and ethnic minority status. While the persons with no HS diploma are also represented in this group being centered just south of the middle of California. When it comes to who would be the most likely to struggle with the social impact of air pollution it looks as though it would be these persons in these areas. Although, having an HS diploma doesn’t make a person inherently more susceptible to air pollution economic mobility is affected.**

### Qu. 2E: MAUP

Think about MAUP and gerrymandering. Explain why is it important to think hard about choosing between the county or census-level SOVI data.

**Depending upon which is used a completely different story could be told. For instance, one could show low levels of something while one doesn’t have enough data to even show up on a graph. If you need a more detailed and localized understanding of social vulnerability, census-level data is likely the better choice. However, if you are looking at larger geographic trends or making high-level policy decisions, county-level data might be sufficient.**

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

- How many monitoring stations are there in the dataset? There are 451 monitoring stations

- What is the Ozone at site name: Beverly Hills-Franklin? Remember units! 33.50973 ppb

- What is the maximum population density? 406.6252

### Qu. 3D. Ozone vs Population Density.

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

**Given the source of the data the ozone ppm seems smaller than what I thought it would be honestly. The majority of the ozone present in a lower population density seems to be counter to what would be expected.**

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

**The ozone is in areas that I would expect it to be as it covers the areas with less population density. In the areas with higher population density, there are lower levels of ozone. This may speak to the levels of people living in the area affecting the level of ozone in the atmosphere.**

1.2020 Database State. https://www.atsdr.cdc.gov/placeandhealth/svi/data_documentation_download.html. Accessed on 11/06/2023.↩︎

## Lab 5: Moran’s I and LISA

### Section A. Set-up

The objective of this lab is to introduce you to the concept of Voronoi polygons, Moran’s I and LISA; building on Lab 4 to look at air pollution in California.

### Libraries

Make sure to install any additional libraries you need

```r
library(tmap)
library(raster)
library(gstat)
library(sf)
library(tidyverse)
library(tigris)
library(plotly)
library(terra)
library(spdep)
```

Reading in the data

I did most of the set up and analysis you did in Lab 4 separately to save computational power for those on R studio cloud. Make sure that the data files from Canvas are in your project folder, then this should just run

## Section B - Voronoi Polygons

You should be able to simply run this code. If not, ask Harman or Dr G.

```r
# you should be able to simply run this code
ozone.terra      <- vect(ozone.sf)
ozone.voronoi.sf <- voronoi(ozone.terra)
ozone.voronoi.sf <- st_as_sf(crop(ozone.voronoi.sf,state.border.terra))

tm_shape(ozone.voronoi.sf) +
               tm_polygons("OZONE_1000PPB",palette="YlGnBu")+
               tm_legend(position = c("right", "top"))+
               tm_layout(main.title="Voroni Tesselation of Ozone in CA (1000 PPB)",
                         main.title.size=.8,main.title.fontface=2)
```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/voronoiCA.png?raw=true)

**In the first graph looking at the Voroni Tesselations, we get a view of the state without the counties to see the values of the ozone across the state. This can give a "truer" image of how the ozone spread looks throughout the state rather than across county lines or at the specific testing sites**

```r
tm_shape(county.data.sf) +
               tm_polygons("OZONE_1000PPB",palette="YlGnBu",breaks=seq(0,100,by=20))+
               tm_legend(position = c("right", "top"))+
               tm_layout(main.title="County Averages of Ozone in CA (1000 PPB)",
                         main.title.size=.8,main.title.fontface=2)

```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/ozoneCounty.png?raw=true)

**In the second graph we get a look at how the makeup of the state is across county lines showing in what counties the ozone is in greater or lower amounts. Although this doesn't give as clear of an image as the Voronoi Tesselations it can give lawmakers and others interested in the issue areas to survey to find what the cause may be as to why the ozone is different in different parts of the state.**

```r
tm_shape(county.data.sf) +
               tm_borders()+
               tm_shape(ozone.sf) +
               tm_dots(col="OZONE_1000PPB",palette="YlGnBu",size=.4,alpha=.6)+
               tm_legend(position = c("right", "top"))+
               tm_layout(main.title="Point values of Ozone in CA (1000 PPB)",
                         main.title.size=.8,main.title.fontface=2)

```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/ozoneLoc.png?raw=true)

**The final graph looks at the issue looking at the individual testing sites. Although this doesn't give a full view of the issue it may add to why we are seeing in the first graph compared to the second. Whether there are more or fewer sensors in an area has the potential to skew what we are seeing in the data. For lawmakers, a recommendation could be to increase the amount of sensors or perhaps even spread them out to other sections where there are few. This could also call into question why the sensors are in the specific locations they are in.**

### Section D. Moran’s I ozone

Here I have provided a shortened version of the code in Dr Gimond’s tutorial. My code is set to look at the ozone levels. Get it running.

```r
# Choose the data you want.  To help your comprehension,
# Switch between this and ozone.voronoi.sf & see how it changes
moran.data <- county.data.sf

# R hates empty polygons, so we're removing them
moran.data <- moran.data[moran.data$OZONE_1000PPB >  0 , ]
tracts_empty  <- st_is_empty(moran.data)
moran.data <- moran.data[which(tracts_empty==FALSE), ]

# make the nearest neighbours and weights matrix
nb <- poly2nb(moran.data, queen=TRUE)
lw <- nb2listw(nb, style="W", zero.policy=TRUE)

# Make the moran scatter plot
moran.plot(moran.data$OZONE_1000PPB, 
           labels=as.character(moran.data$COUNTY),
           listw= lw,
           xlab = "Value of Ozone per polygon (1000 PPB)",
           ylab = "Average Ozone in neighbouring polygons (1000 PPB)",
           zero.policy = T)

```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/avgOzone.png?raw=true)

```r
# And conduct the moran hypothesis test
moran.test(moran.data$OZONE_1000PPB, listw= lw)

```

```r
## 
##  Moran I test under randomisation
## 
## data:  moran.data$OZONE_1000PPB  
## weights: lw    
## 
## Moran I statistic standard deviate = 5.7038, p-value = 5.858e-09
## alternative hypothesis: greater
## sample estimates:
## Moran I statistic       Expectation          Variance 
##       0.500936524      -0.018867925       0.008305187
```

```r
plot(moran.mc(moran.data$OZONE_1000PPB, lw, nsim=999, alternative="greater"))

```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/moranI.png?raw=true)

### Interpretation

**HO: The average ozone in neighboring polygons (1000PPB) caused the value of ozone per polygon (1000PPB)**

**H1: The actual sample is different from the expected if H0 was true**

**Test statistic: 5.7038 p-value: 5.858e-09**

**Interpretation: Looking at the scatter plot the data seems to be independent. With there also being some clustering within the neighborhood and the ozone value per polygon. With a p-value so low we can reject the H0 hypothesis as there is sufficient evidence that there is higher than average ozone in surrounding polygons. With our Moran’s I statistic being 5 we have a strong positive autocorrelation here.**

### Monte Carlo

**The Monte Carlo approach takes a point and does an independent random process with polygons around it using an independent random process and seeing how likely it is.**

### A different variable

Then, copy and edit my code to conduct a Moran’s I analysis of a variable (column) of your choice and interpret your findings in the text. Remember, you can go back to the lab 4 description to work out what columns are showing you. And names (moran.data) will show you the column names.

### Section E. LISA

My code for the LISA analysis should just run. I have split it into several code chunks to make it easier for the cloud and to explain what is happening. Get them all running then see below for questions.

```r

#---------------------------------------------------
# We can either create the Raw LISA values using the theoretical approach or Monte Carlo
# 
#  - Padjust allows us to account for mulitple testing issues.
#  - adjust.x allows us to omit polygons with no neighbours as needed
#  - zero.policy allows us to keep but ignore polygons with no neighbours
#  - nsim: number of simulations, If this crashes the cloud try reducing to say 50
#---------------------------------------------------
LocalMoran_Output <- localmoran_perm(x = moran.data$OZONE_1000PPB, 
                                     listw = lw, 
                                     nsim=499,
                                     adjust.x = TRUE,
                                     zero.policy = T)
```

```r
#---------------------------------------------------
# The column names are less intuitive to newbies, so let's rename them 
# (to see the originals, type ?localmoran into the console)
# The skew and kurtosis are because the histogram of IRP I might not look normal
# To do this I force the output to be a standard dataframe
#---------------------------------------------------
LocalMoran_Table <- as.data.frame(LocalMoran_Output)
names(LocalMoran_Table) <- c("Observed_LocalI", "IRP_estimate_LocalI",   "Variance_I",
                             "ZScore_I",  "PValue_Theoretical",  "PValue_MonteCarlo",
                             "Skew_I","Kurtosis_I")

#---------------------------------------------------
# Merge with our data
#---------------------------------------------------
moran.data <- cbind(moran.data,LocalMoran_Table[,4:6])

#---------------------------------------------------
# It turns out that the quadrants are secretly stored in the output, 
# so we don't even have to manually calculate them
#---------------------------------------------------
moran.data$LISA_Quadrant <- attr(LocalMoran_Output,"quadr")$mean

```

```r
#---------------------------------------------------
# Remove polygons that are unlikely to be significant
# YOU GET TO CHOOSE THE THRESHOLD HERE
# Find the rows where your p-value is > your threshold
#---------------------------------------------------
critical_threshold <- 0.05
RowsOverThreshold <- which(moran.data$PValue_MonteCarlo > critical_threshold) 

#---------------------------------------------------
# Make the column a character to make life easy
# Then rename the quadrant in those rows p>0.05, or 
# whatever your threshold is
#---------------------------------------------------
moran.data$LISA_Quadrant_Plot <- as.character(moran.data$LISA_Quadrant)
moran.data$LISA_Quadrant_Plot[RowsOverThreshold] <- paste("P>",critical_threshold,sep="")
```

```r

#---------------------------------------------------
# Make a factor again
#---------------------------------------------------
moran.data$LISA_Quadrant_Plot <- factor(moran.data$LISA_Quadrant_Plot,
                               levels= c("High-High","High-Low", 
                                         "Low-High", "Low-Low",
                                         paste("P>",critical_threshold,sep="")))
```

```r

#---------------------------------------------------
# And make a plot
#---------------------------------------------------
mapLISA <- 
  tm_shape(moran.data)  +
  tm_fill( "LISA_Quadrant_Plot",id="NAME",  alpha=.6,
           palette=  c("#ca0020","#f4a582","#92c5de","#0571b0","white"), title="") +
  tm_borders(alpha=.5) +
  tm_legend(position = c("left", "bottom"))+
  tm_layout(title = paste("LISA,sig= ",critical_threshold), title.position = c("right", "top"))


#---------------------------------------------------
# compare against the raw values 
#---------------------------------------------------
mapRaw <- tm_shape(moran.data) + 
  tm_fill(col = "OZONE_1000PPB", 
              style = "pretty",
              palette = "YlGnBu", 
              border.alpha = 0, 
              title = "",alpha=.6) +  
  tm_layout(title = "Mean Ozone", title.position = c("right", "top"))+
  tm_borders(alpha=.5)+
  tm_legend(position = c("left", "bottom"))
  


tmap_options(check.and.fix = TRUE)
tmap_mode("plot")
tmap_arrange(mapRaw,mapLISA)
```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/lisa.png?raw=true)

```r
# Choose the data you want.  To help your comprehension,
# Switch between this and ozone.voronoi.sf & see how it changes
moran.data1 <- county.data.sf

# R hates empty polygons, so we're removing them
#Change the data value to be EP_Age65 to look at the elderly 
moran.data1 <- moran.data1[moran.data1$EP_POV150 >  0 , ]
tracts_empty  <- st_is_empty(moran.data1)
moran.data1 <- moran.data1[which(tracts_empty==FALSE), ]

# make the nearest neighbours and weights matrix
nb1 <- poly2nb(moran.data1, queen=TRUE)
lw1 <- nb2listw(nb1, style="W", zero.policy=TRUE)

# Make the moran scatter plot
#include labels showing whats happening 
moran.plot(moran.data1$EP_POV150, 
           labels=as.character(moran.data1$COUNTY),
           listw= lw1,
           xlab = "estimate of pop 65 +",
           ylab = "Average elderly in neighbouring polygons ",
           zero.policy = T)

```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/avg65.png?raw=true)

```r
# And conduct the moran hypothesis test
# To show the autocorrelation 
moran.test(moran.data1$EP_POV150, listw= lw1)
```

```r
## 
##  Moran I test under randomisation
## 
## data:  moran.data1$EP_POV150  
## weights: lw1    
## 
## Moran I statistic standard deviate = 4.0864, p-value = 2.191e-05
## alternative hypothesis: greater
## sample estimates:
## Moran I statistic       Expectation          Variance 
##       0.327435936      -0.017543860       0.007127149
```

```r
plot(moran.mc(moran.data1$EP_POV150, lw1, nsim=999, alternative="greater"))

```

![SOVI_Graphs](https://github.com/darkawesome/blog/blob/main/content/img/Voronoi/densityMo.png?raw=true)

### Interpretation

Using the lecture notes and readings interpret what the maps are showing you. Your write up should include

- What do each of the four colors/quadrants mean?

  **The colors for the mean ozone show the ozone levels and separate the breaks based on the abundance of the ozone in the different California counties. For the LISA the colors are showing the higher than average areas within the cluster that are surrounded by high ozone beyond the critical threshold (0.05). Likewise the same is true for the lower-than-average areas that are surrounded by low ozone where nothing is.**

- How do they link to the Moran scatterplot?

  **Looking at the scatter plot the map on the right shows our outliers and points deep within the cluster. While the one on the left looks more at the cluster itself with all of the points within it.**

Change the critical threshold to 0.01, and to 0.1 and to 0.5. Each time, re-run the LISA code (everything including and below the critical code chunk, or run-all). Explain what is happening in terms of ozone pollution in California.

**When we change the point at which we have our critical threshold we effectively “move the goal post” farther or closer. When going lower to 0.01 the higher bar leaves less at the edges of our cluster as our confidence interval is a lot larger. When we go higher to 0.1 the confidence interval is much smaller and more areas appear on the LISA map.**
