---
title: "GeologyAnalysis"

date: 2023-11-02T00:13:09-04:00
url: /GeologyAnalysis/
image: /images/2020-thumbs/GeologyAnalysis.jpg
categories:
  - Data Science

draft: false
---

 A Focus on the Trails of Pennsylvania 

<!--more-->




### Abstract 

*This Report is an in-depth look at the trails within PA from a networking perspective considering how much of an effect the environment may have on the network. Additionally I will be looking at how the points interact with one another and the centrality of the network.*

```r
# Stop warnings printing
knitr::opts_chunk$set(echo = TRUE, warning=FALSE, message = FALSE)
```

```r

# Data manipulation libraries
library(sp)
library(sf)
library(tidyverse)
library(tmap)
library(spatstat)

# Plotting
library(leaflet)

# Machine Learning libraries
library(rpart.plot)
library(RColorBrewer)
```


## Introduction 
Intended Audience: PA state park rangers and State members of the general assembly. I have been asked to provide a spatial analysis of trails in the State, to increase tourism, and make maintenance more efficient. Deploying AI-driven solutions to enhance the state's public recreational infrastructure. Here's how this could benefit a state:

Maintenance Efficiency: AI can help streamline the maintenance of state-owned trails by predicting and prioritizing repair needs. This ensures that limited resources are allocated effectively, reducing downtime and improving the safety of the trails.

Tourism Promotion: Using AI-powered mobile apps, the state can provide personalized trail recommendations to visitors based on their interests and fitness levels. This promotes tourism, boosting the local economy and increasing revenue from visitor fees.

Safety and Emergency Response: AI can be used to monitor trail conditions in real-time, identify safety hazards, and facilitate faster emergency responses in case of accidents or natural disasters, ensuring the well-being of trail users.

Overall, integrating AI into a state's trail network management enhances the quality of recreational experiences, supports local economies, and promotes responsible environmental stewardship. This can contribute to a positive image for the state and an improved quality of life for its residents and visitors.


## Data Summary 

These points relate to the trails in the state of Pennsylvania. The data was collected using GPS units and then checked against aerial imagery between November 2007 and October 2009. With updates being submitted by users and those being checked and updated regularly.

Topic/Source: Pennsylvania Spatial Data Access (PASDA) is Pennsylvania's official public access open geospatial data portal (1). 

Their are 2466 rows with 15 columns. However I will only be using 7 of those columns in my analysis. I think some missing data would be where the appalachain trail goes through pennsylvania and some other trails that cross state borders. The initial map I have of the area kind of shows this but if you had no knowledge of this area you wouldn't know why that pattern was there. 

## Reading in Data

```r

data <-read.csv("ExplorePAtrails_WP201703.csv")

```

## Data & Spatial Wrangling


```r

df <- subset(data, select = -c(NAME02, NAME03,ADA_ACCESS,ACCT_ID,COMMENTS,SUBTYPE_CO,LAT,LNG))
```

These columns were taken out of the final dataframe to remove redundancy, and because the columns that weren't adding to the analysis. 


```r

summary(df)
```


## Data Structure 

- OBJECTID : Internal feature number for where the trail is. 
- NAME01 : The trail name 
- COUNTY : The county the trail is in 
- UPDATE : Tells the last time it was updated written in a year-month-day-time format
- LATITUDE : Gives the latitude of the point
- LONGTITUDE : Gives the longitude of the point
- TRAILID : Internal feature number 



![Scatter plot](https://gcdnb.pbrd.co/images/jgiB56bcxRxd.png?o=1 "PA Trails Point Map")

## Exploratory Analysis 

Looking at the map before analysis there is a pattern that follows the Applachain trail through the state. 

Each point in the data sets a point along the line feature for the trials in Pennsylvania. The data presented has been collected since November 2007 and has been updated since October 2009. There are 2466 data points in the data frame. With 15 columns in the original data frame and 7 in the data frame after data wrangling. 

```r
mapdata<-data.frame(
  Longitude = df$LONGITUDE,
  Latitude = df$LATITUDE)

map <- leaflet(mapdata) %>%
  addTiles() %>%
  addMarkers(
    lng = ~Longitude,
    lat = ~Latitude
  )
```
This graph was done to get the points into a from that makes an interactive map.

```r

map
```

```r
plot(mapdata)

```

### K-Means Clustering
```r

set.seed(123)
data <- mapdata

# Perform K-Means clustering with 10 clusters 
kmeans_result <- kmeans(mapdata, centers = 10)

# View the clustering results
print(kmeans_result)

# Cluster assignments for each data point
cluster_assignments <- kmeans_result$cluster

# Cluster centroids
centroids <- kmeans_result$centers
```


```r

# Create a scatter plot with points colored by cluster
library(ggplot2)
data_plot <- data.frame(data, Cluster = factor(kmeans_result$cluster))
ggplot(data_plot, aes(x = df$LONGITUDE, y = df$LATITUDE, color = Cluster)) +
  geom_point() +
  labs(title = "K-Means Clustering")

```


![K-Means Clusteriing](https://gcdnb.pbrd.co/images/6Q04XT3UHugx.png?o=1 "K-Means Cluster")

The following K-means clustering has 10 clusters of sizes 41, 554, 235, 231, 90, 343, 327, 198, 217, 130.

```r
new_data <- data.frame(x = mapdata)
model <- kmeans(data, centers = 10)

```

```r
wcss <- sum(kmeans_result$withinss)
wcss

```

 A WCSS value of 473.5104 suggests that the data points are relatively close to the centroids within each cluster. With further analysis needed to find an elbow point or point
 where the point the reduction in WCSS starts to level off. Which tells us about the diminishing returns in terms of clustering quality as k increases.
 
 With this point found an optimal distribution of trails can be made to have more dispersion throughout the state. However, given that the points we are using are taken from line graphs further analysis into the cluster would not be needed as the correlation would be inherent. Moving forward I will look at other factors across the state as it relates to the trail points. 

## Analyzing the Network 
The Methodology behind hte work below is from this paper on Spatial Statictics by Adrian Baddeley, Gopalan Nair, Suman Rakshit, Greg McSwiggan,Tilman M. Davies(2). In which they analyze research on the spatial analysis of events that occur along a network of lines.  

## Convertiing to Spatial Data 

```r
trails.sf  <- st_as_sf(data,coords=c("Longitude","Latitude"),crs=4269)
# change the map projection 
trails.utm <- st_transform(trails.sf,crs=4269)

## This is used for making the trail into a network later on 
trail_utm_pts <- st_coordinates(trails.utm)

```
## Pennsylvania Trails and Socioeconomic Status

```r

# reading in the libraries
library(tmap)
library(raster)
library(gstat)
library(sf)
library(tidyverse)
library(tigris)
library(plotly)
```

```r
## breaks the project into parts leaving less in the environment
## helps focus on the current dataframes 
## rm(list=ls())

# Read in the Pennsylvania county & census tract boundaries from Tigris
county.border <- counties("PA", cb = TRUE, resolution = "20m")
county.border <- county.border[,which(names(county.border) %in%  c("GEOID",
                                                                   "geometry"))]
tract.border <- tracts("PA", cb = TRUE)
tract.border <- tract.border[,which(names(tract.border) %in%  c("GEOID","geometry"))]

# Download the Pennsylvania SOVI county data from the internet and tidy up
download.file("https://svi.cdc.gov/Documents/Data/2020/csv/states/Pennsylvania.csv", destfile="SOVI2020_PA_Tract.csv")
tract.sovi     <- read.csv("SOVI2020_PA_Tract.csv")
names(tract.sovi)[which(names(tract.sovi) %in% "FIPS")] <- "GEOID"
tract.sovi <- tract.sovi[,-grep("M_",names(tract.sovi))]
tract.sovi <- tract.sovi[,-grep("MP_",names(tract.sovi))]
tract.sovi <- tract.sovi[,-grep("SPL_",names(tract.sovi))]
tract.sovi <- tract.sovi[,-grep("EPL_",names(tract.sovi))]
tract.sovi <- tract.sovi[,c(1:49,69)]
tract.sovi <- tract.sovi %>% mutate_all(~ ifelse(. == -999, NA, .))


# Download the Pennsylvania SOVI tract data from the internet and tidy up
download.file("https://svi.cdc.gov/Documents/Data/2020/csv/states_counties/Pennsylvania_county.csv", destfile="SOVI2020_PA_County.csv")
county.sovi <- read.csv("SOVI2020_PA_County.csv")
names(county.sovi)[which(names(county.sovi) %in% "FIPS")] <- "GEOID"
county.sovi <- county.sovi[,-grep("M_",names(county.sovi))]
county.sovi <- county.sovi[,-grep("MP_",names(county.sovi))]
county.sovi <- county.sovi[,-grep("SPL_",names(county.sovi))]
county.sovi <- county.sovi[,-grep("EPL_",names(county.sovi))]
county.sovi <- county.sovi[,c(1:48,69)]
county.sovi <- county.sovi %>% mutate_all(~ ifelse(. == -999, NA, .))

```

```r
## Had to convert this to a charater as rather than a integer to avoid error
# Convert GEOID column in tract.sovi to character
tract.sovi$GEOID <- as.character(tract.sovi$GEOID)
county.sovi$GEOID <- as.character(county.sovi$GEOID)
# IGNORE THE WARNING!
# Merge the sovi data and the census tract borders
tract.sovi.sf  <- geo_join(tract.border ,tract.sovi ,by="GEOID")
county.sovi.sf <- geo_join(county.border,county.sovi,by="GEOID")

# Finally change the map projection to 
# the the North American Albers Equal Area Conic projection (AEA)
tract.sovi.sf  <- st_transform(tract.sovi.sf,3310)
county.sovi.sf <- st_transform(county.sovi.sf,3310)

```
```r
# Making a map of Socioeconomic status with where the points for the trails are
# on a map of the different censes tracts 

map1 <- qtm(county.sovi.sf,dots.col="RPL_THEME1",dots.size=.6,dots.palette="Greens")+
        tm_shape(trails.sf)+ tm_dots(size=.05,col="red")+
        tm_shape(tract.sovi.sf)+tm_borders()
map2 <- qtm(county.sovi.sf,dots.col="RPL_THEME3",dots.size=.6,dots.palette="Blues")+
        tm_shape(trails.sf)+ tm_dots(size=.05,col="red")+
        tm_shape(tract.sovi.sf)+tm_borders()
map3 <- qtm(county.sovi.sf,dots.col="EP_NOHSDP",dots.size=.6,dots.palette="Oranges")+
        tm_shape(trails.sf)+ tm_dots(size=.05,col="red")+
        tm_shape(tract.sovi.sf)+tm_borders()
map4 <- qtm(county.sovi.sf,dots.col="EP_MUNIT",dots.size=.6,dots.palette="Purples")+
        tm_shape(trails.sf)+ tm_dots(size=.05,col="red")+
        tm_shape(tract.sovi.sf)+tm_borders()
tmap_arrange(map1,map2,map3,map4)
```

<a href="https://ibb.co/chCBHRB"><img src="https://i.ibb.co/jkWN2qN/RPLTHeme1.png" alt="RPLTHeme1" border="0"></a>

Looking at the socioeconomic status of the points across the census tracts as it relates to the trails we can not see any real disparities. Nor anything to suggest that areas with higher or lower socioeconomic status have a higher amount of trail near them.There may be other factors playing a role here like edge effects, ecological factors or even just different clusters of where people live. And this holds true across all of the other predictive factors looked at like Racial and Ethnic Minority Status.

```r
## Moran's I AutoCorrelation
library(spdep)

nb <- poly2nb(tract.sovi.sf, queen=TRUE)
lw <- nb2listw(nb, style="W", zero.policy=TRUE)

# Get the average neighbor Housing in buildings with 10 or more units for each polygon
inc.lag <- lag.listw(lw, tract.sovi.sf$EP_MUNIT)
inc.lag
```

```r
plot(inc.lag ~ tract.sovi.sf$EP_MUNIT, pch=16, asp=1)
M1 <- lm(inc.lag ~ tract.sovi.sf$EP_MUNIT)
abline(M1, col="blue")
```

<a href="https://ibb.co/H4vxnt9"><img src="https://i.ibb.co/ZXsYWBb/moransI.png" alt="moransI" border="0"></a>
```r
# Conduct the Moran's I hypothesis test
moran.test(tract.sovi.sf$EP_MUNIT, listw= lw)
```


Moran I test under randomisation
 
data:  tract.sovi.sf$EP_MUNIT  
weights: lw    
Moran I statistic standard deviate = 37.838, p-value < 2.2e-16

alternative hypothesis: greater
sample estimates:

Moran I statistic   0.3863926463    
Expectation   -0.0002903600     
Variance  0.0001044349

```r
plot(moran.mc(tract.sovi.sf$EP_MUNIT, lw, nsim=999, alternative="greater"))

```
<a href="https://ibb.co/H4vxnt9"><img src="https://i.ibb.co/ZXsYWBb/moransI.png" alt="moransI" border="0"></a>

### Interpretation:
Moran’s I Statistic (0.3864):

The positive value (0.3864) indicates a positive spatial autocorrelation, suggesting that similar values tend to be close to each other in space.Which we previously have seen with the data points for the trails in the state.
Standard Deviate (37.838):

The high standard deviate indicates that the observed Moran’s I value is significantly different from what we would expect under the assumption of spatial randomness. This is further supported by the very low p-value.
### P-value (2.2e-16):

The extremely low p-value (2.2e-16) indicates strong evidence against the null hypothesis of spatial randomness. In practical terms, it suggests that the spatial pattern observed is unlikely to be due to random chance.The alternative hypothesis being “greater” suggests that the observed spatial autocorrelation is greater than what would be expected under the assumption of spatial randomness.
Interpretation of a Moran’s I Plot:

Clustering in the Bottom Left Corner: This suggests local clusters of similar values. Meaning that neighboring areas tend to have similar values, contributing to positive spatial autocorrelation.

In summary, the results suggest a significant positive spatial autocorrelation, meaning that similar values are spatially clustered.

```r
#testdata is the name of my variable. Yours will be the census tract or county sovi sf data.  ColumnName is the name of the column you want to color
map1 <- qtm(county.sovi.sf,fill="RPL_THEME1",fill.palette="Greens") #Socioeconomic Status theme summary
map2 <- qtm(county.sovi.sf,fill="RPL_THEME3",fill.palette="Blues") #Racial and Ethnic Minority Status 
map3 <- qtm(county.sovi.sf,fill="EP_NOHSDP",fill.palette="Reds") #Persons with no HS Diploma 
map4 <- qtm(county.sovi.sf,fill="EP_MUNIT",fill.palette="Purples") #Housing in buildings with 10 or more units 
tmap_arrange(map1,map2,map3,map4)
```

<a href="https://ibb.co/T4XKJ0k"><img src="https://i.ibb.co/McT8K1s/sovimap.png" alt="sovimap" border="0"></a>

## Map Interpretation
### Socioeconomic Status

Looking at the socioeconomic status across the state there are many areas of high socioeconomic status surrounded by low. Though a trend that we can see here is that the northern half of the state is more well of then the southern half. Though there are some counties that are “islands” were they are surrounded by better off counties.
### Racial and Ethnic Minority Status

The racial and ethnic minority status is similar as far as the “islands” are concerned. Though this time two of the counties have a higher racial & ethnic minority status than those surrounding them. With there being a cluster in the eastern and south eastern bit of the state. With the remainder of the state being quite dispersed until the border with Ohio which shows a fair bit of racial dispersion. Perhaps hinting at edge effects that may be because of how the state is overall. Or it may be because of the proximity of Pittsburgh and it being a major city in the state.
### Persons with no HS Diploma

This map shows a lot of interesting information where we see in Centre county and those counties around Pittsburgh having a lot of people with high school diplomas. Though in Lancaster, Juniata, and Forest counties have the highest amount of people with a high school diploma. This is could be because of the amount of colleges in the areas or perhaps the renown that the colleges in the area have.
### Housing in buildings with 10 or more units

Here as though our highest counties in this variable are in Centre county , around Philadelphia and Pittsburgh. Additionally, we do see a lot of clustering in some areas on the south east and south west edges of the state. Perhaps again attributed to edge effects from the surrounding states. With close proximity to New Jersey and Delaware some residents may be choosing to live in Pennsylvania to reap its benefits and not have to pay those in other states.

```r
# Adding in the Age & Sex data
Pa.AgeSex <-read.csv("American Community Survey2020(Age&SEX).csv")

## editing the GEOID column so it can merge in with the tract spatial data
Pa.AgeSex$GEOID <- sub("1400000US", "", Pa.AgeSex$GEOID)

## merging the frames together
tract.sovi.sf1  <- geo_join(tract.border ,Pa.AgeSex ,by="GEOID")
```

```r 
### Estimate of the total population that is 18 years and older 
map5 <- qtm(tract.sovi.sf1,dots.col="S0101_C01_026E",dots.size=.6,dots.palette="Greens")+
        tm_shape(trails.sf)+ tm_dots(size=.05,col="red")+
        tm_shape(tract.sovi.sf)+tm_borders()
### Estimate of the total population that is 60 years and older
map6 <- qtm(tract.sovi.sf1,dots.col="S0101_C01_028E",dots.size=.6,dots.palette="Blues")+
        tm_shape(trails.sf)+ tm_dots(size=.05,col="red")+
        tm_shape(tract.sovi.sf)+tm_borders()

tmap_arrange(map5,map6)
```

<a href="https://ibb.co/ydvyfgH"><img src="https://i.ibb.co/jb0M63n/sovitrail.png" alt="sovitrail" border="0"></a>



