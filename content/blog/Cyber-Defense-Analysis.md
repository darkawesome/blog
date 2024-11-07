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
draft: false
---

Exploratory Analysis of the Cyber Events Database that collects publicy available information on cyber events, from 2014 to present.
<!--more-->

After placing out of the R class in my grad school I am now looking to do something to occupy my time and learn more. I decided to use this database as its actually something interesting that looks at country level events. More importantly its part of the reason as to why I wanted to come to this school. 

This analysis focuses specifically on the states that were attacked. Further analysis will look into countries that were attacking. 

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

![Summary data](https://github.com/darkawesome/blog/blob/main/content/img/image.png?raw=true)

Simple plot looking at during what months these instances happen (need to confirm this with the codebook)

```r
ggplot(df, aes(x=month)) + geom_bar(fill='seagreen') +
  labs(x='Months') 
```

![months events occur](https://github.com/darkawesome/blog/blob/main/content/img/cyberMonths.png?raw=true)

Simple plot looking at during what typical actors are conducting this activity (need to confirm this with the codebook)
```r
ggplot(df, aes(x=actor_type)) + geom_bar(fill='coral2') +
  labs(x='Actor Types') 
```

![Actor Types](https://github.com/darkawesome/blog/blob/main/content/img/actorTypes.png?raw=true)


Simple plot looking at what the typical event types are (need to confirm this with the codebook)
```r
ggplot(df, aes(x=event_type)) + geom_bar(fill='steelblue') +
  labs(x='Event Types') 
```
![Event Types](https://github.com/darkawesome/blog/blob/main/content/img/Event-Types.png?raw=true)


Here I used the rnaturalearth package to get geometries (spatial data) for countries. This package provides access to Natural Earth datasets, which include country geometries (polygons).
```r
# install.packages("rnaturalearth")
library(rnaturalearth)

```


```r
# function to retrieve country geometries
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



Problem with an invalid point dealing with Telecommunications companies in Sudan. Will try to fix later. I was hung up on this longer than 30mins and it was the only thing holding back a graph. I skipped this....
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

![World Map by motive](https://github.com/darkawesome/blog/blob/main/content/img/worldAttackedMotivemap.png?raw=true)

```r
tm_shape(world_sf_valid) + 
  tm_borders() +  # Draw borders
  tm_fill(col = "event_type", palette = "Set3", title = "Motive") + # Color by 'Event Type' variable
  tm_layout(main.title = "World Map by Event Type",
            legend.outside = TRUE)

```

![World Map by Event Type](https://github.com/darkawesome/blog/blob/main/content/img/WorldEventMap.png?raw=true)


## Conclusion

The types of actors and events seem intriguing and worth deeper exploration, particularly how attack locations and patterns may have shifted over time. For example, has the United States always been a primary target for financial attacks? The following are notes to guide my future analysis:

- **Two Geometry Data Frames**: One for the attacker and one for the target.
  - The target data frame is complete for now.
- **Spatial Clustering**: This will help identify hotspots and determine if attacks are regionally concentrated, affecting either the attackers or those attacked.
  - Consider adding **Moran's I** to further analyze spatial autocorrelation.
- **Machine Learning Models**: Random forests and decision trees could provide insight into predictive trends. Or even can be used to predict future outcomes or changes in a variable.

Another potential direction would be a stacked bar graph to visualize event types, with subtypes layered on top. This approach could facilitate cross-variable analysis, revealing interactions between factors like industry, motive, and actor type.

To support more rigorous statistical analysis, I plan to investigate whether observed patterns are random or if they indicate a larger trend. Importantly, I'll explore predictive models (e.g., using `predict`) to see if it’s feasible to estimate the location or target of future attacks. While accuracy may be a challenge given the complex motivations behind cyber-attacks, this model could offer valuable insights into emerging risks.

Citation
 Harry, C., & Gallagher, N. (2018). Classifying Cyber Events. Journal of Information Warfare, 17(3), 17-31.



