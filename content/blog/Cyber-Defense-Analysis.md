---
title: "Cyber Defense Analysis"

date: 2024-11-07T12:29:26-05:00
url: /Cyber-Defense-Analysis/
image: /images/2020-thumbs/Cyber-Defense-Analysis.jpg
categories:
  - Linux
  - Windows
  - Networking
tags:
  - Ubuntu
draft: true
---

Exploratory Analysis of the Cyber Events Database that collects publicy available information on cyber events, from 2014 to present.
<!--more-->

After placing out of the R class in my grad school I am now looking to do something to occupy my time and learn more. I decided to use this database as its actually something interesting that looks at country level events. More importantly its part of the reason as to why I wanted to come to this school. 

This analysis focuses specifically on the states that were attacked. Further analysis will look into countries that were attacking. 

---
title: "R Notebook"
output: html_notebook
---

 ```r
# Data manipulation libraries
library(sp)
library(sf)
library(spatstat)


# Plotting
library(leaflet)
library(ggplot2)
library(tmap)

library(sf)
library(terra)
library(dplyr)
library(spData)
```


```r
library(readxl)
data <- read_excel("E:/RStudio/Rcode/umspp-export-2024-08-26(1).xlsx")

df <- subset(data, select= -c(slug,description,source_url))

```


  
```r

summary(df)

```

![Summary data](https://github.com/darkawesome/blog/blob/main/content/img/image.png)

Simple plot looking at during what months these instances happen (check the codebook on this)

```r
ggplot(df, aes(x=month)) + geom_bar(fill='seagreen') +
  labs(x='Months') 
```

![months events occur](https://github.com/darkawesome/blog/blob/main/content/img/image-1.png)

Simple plot looking at during what typical actors are conducting this activity (check the codebook on this)
```r
ggplot(df, aes(x=actor_type)) + geom_bar(fill='coral2') +
  labs(x='Actor Types') 
```

![Actor Types](https://github.com/darkawesome/blog/blob/main/content/img/actorTypes.png)


Simple plot looking at what the typical event types are (check the codebook on this)

```r
ggplot(df, aes(x=event_type)) + geom_bar(fill='steelblue') +
  labs(x='Event Types') 
```
![Event Types](https://github.com/darkawesome/blog/blob/main/content/img/Event-Types.png)

actor type, event type seems to be something interesting to look at further. 2 geometry data frames 1 for attacker and 1 for the attacked. 
- the attacked is done 

rnaturalearth package to get geometries (spatial data) for countries. This package provides access to Natural Earth datasets, which include country geometries (polygons).
```r
# install.packages("rnaturalearth")
library(rnaturalearth)

```

function to retrieve country geometries:
```r
# Get country geometries using rnaturalearth
countries_geom <- ne_countries(scale = "small", returnclass = "sf")

# Merge the geometries with the country names
countries_sf <- countries_geom %>%
  filter(name %in% df$country) %>%         # Filter for countries in your dataframe
  left_join(df, by = c("name" = "country")) # Join with your dataframe

# Set the CRS (WGS84 is EPSG:4326, which is common for world maps)
countries_sf <- st_set_crs(countries_sf, 4326)
```


```r
# getting rid of bloat in the sf dataframe
world_sf <- countries_sf %>%
  select(event_date, year, month, actor, actor_type, organization, industry_code, 
         industry, motive, event_type, event_subtype, actor_country,geometry)
```


Problem with an invalid point dealing with Telecommunications companies in Sudan. Will try to fix later
```r
invalid_geometries <- world_sf[!st_is_valid(world_sf), ]
print(invalid_geometries, n = Inf)
```

```r
## getting rid of invalid polygon (might come back and figure out why this is wrong)
world_sf_valid <- world_sf[st_is_valid(world_sf), ]

```

```r
tm_shape(world_sf_valid) + 
  tm_borders() +  # Draw borders
  tm_fill(col = "motive", palette = "Set3", title = "Motive") + # Color by 'motive' variable
  tm_layout(main.title = "World Map by Motive",
            legend.outside = TRUE)

```

![World Map by motive](https://github.com/darkawesome/blog/blob/main/content/img/worldAttackedMotivemap.png)

```r
tm_shape(world_sf_valid) + 
  tm_borders() +  # Draw borders
  tm_fill(col = "event_type", palette = "Set3", title = "Motive") + # Color by 'Event Type' variable
  tm_layout(main.title = "World Map by Event Type",
            legend.outside = TRUE)

```

![World Map by Event Type](https://github.com/darkawesome/blog/blob/main/content/img/WorldEventMap.png)


## Conclusion


Citation
 Harry, C., & Gallagher, N. (2018). Classifying Cyber Events. Journal of Information Warfare, 17(3), 17-31.



