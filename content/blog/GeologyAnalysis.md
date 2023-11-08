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

Topic/Source: Pennsylvania Spatial Data Access (PASDA) is Pennsylvania's official public access open geospatial data portal. 

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
 
 With this point found an optimal distribution of trails can be made to have more dispersion throughout the state.



## Conclusion

This post is still in development as the project continues in the class I am taking. 



