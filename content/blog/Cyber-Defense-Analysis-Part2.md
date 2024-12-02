---
title: "Cyber Defense Analysis Part2"

date: 2024-11-19T21:55:25-05:00
url: /Cyber-Defense-Analysis-Part2/
image: /images/2020-thumbs/Cyber-Defense-Analysis-Part2.jpg
categories:
  - Linux
  - Windows
  - Networking
tags:
  - Ubuntu
draft: true
---
This is a continuation of the previous work done by Dimitri Nilov (https://www.gotechinsights.com/blog/part2cybertrends). 
<!--more-->

In this portion of the Cyber Defense Database research I have done a time series Analysis of the data from 2014 - 2022.


```r
# Data manipulation libraries
library(sp)
library(sf)
library(spatstat)
library(dplyr)

# Plotting
library(leaflet)
library(ggplot2)
library(tmap)
library(sf)
library(terra)
library(spData)

library(gganimate)
```

```r
library(readxl)
data <- read_excel("E:/RStudio/Rcode/umspp-export-2024-08-26(1).xlsx")

df <- subset(data, select= -c(slug,description,source_url,industry_code))
```

```r
summary(df)
```

![Summary](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/summary.png?raw=true)

```r
table1 <- table(df$motive, df$actor_type, 
                df$event_type)

ftable(table1)
```

![table1](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/table1.png?raw=true)
```r
# FREQUENCY MARGINALS
# row marginals - totals for each marital status across gender
margin.table(table1, 2)
```

![table2](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/table2.png?raw=true)


```r
# colum marginals - totals for each gender across marital status
margin.table(table1, 1)
```

![MarginTable1](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/MarginTable1.png?raw=true)


## 3 Way Table 
Looking at the table of the motives, actor type and event type we can see that a lot of the attacks have a financial motive. With it being the combination with the greatest distribution further analyisis could reveal more on why. Or perhaps show when this became an issue or if it always was an issue. Protest-hacktivist and Undetermined-Criminal would be the other groups that have a good amount of a distribution among the attacks. Although it doesn’t have the same levels of distribution these are also areas that can show more information. 

```r
ggplot(df, aes(x = (event_type))) +
        geom_bar(aes(y = after_stat(count)/sum(after_stat(count)))) +
        xlab("Event Types") + ylab("Count") +
        facet_grid(~actor_type) + geom_bar(fill='coral2') +
        theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

![Event Types Histogram](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/EventTypeHist.png?raw=true)



## Event Type histogram
This histogram omits the motives and just shows the event types and actor types. At first glance we can see that the majority of attacks are criminal in nature. Looking at the histogram similar findings can be seen here. Although, now we can see that attacks that attributed to nation-states also have occurred quite a bit. (stopped here)



```r
ggplot(df, aes(x=month)) + geom_bar(fill='seagreen') +
  labs(x='Months') 
```


![Monthly Attacks](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/Monthly.png?raw=true)

## Attacks per Month
Looking at this histogram we can see no seasonality among the data. Nor can we see any times in which the attacks spike relative to the normal distribution of data. January, March and August being the peaks do show some times where the events may be taking place more often across the years. However,looking at the surrounding months some fallacies could be apparent here. Mainly being the edge effects from when an event was coded to have occured. This has pushed me to look more at the years rather than the months of when these attacks occur.

```r
# install.packages("rnaturalearth")
library(rnaturalearth)

# rnaturalearth package to get geometries (spatial data) for countries. This package provides access to Natural Earth datasets, which include country geometries (polygons).

```

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
A problem was caught here with a value being coded incorrectly in the excel sheet. The single data point was dealing with Telecommunications companies in Sundan.

```r
invalid_geometries <- world_sf[!st_is_valid(world_sf), ]
print(invalid_geometries, n = Inf)

```

```r
## getting rid of invalid polygon (might come back and figure out why this is wrong)
world_sf_valid <- world_sf[st_is_valid(world_sf), ]

```

```r
## this takes a minute to load 

tm_shape(world_sf_valid) + 
  tm_borders() +  # Draw borders
  tm_fill(col = "motive", palette = "Set3", title = "Motive") + # Color by 'motive' variable
  tm_layout(main.title = "World Map by Motive",
            legend.outside = TRUE)

```


![World Map by Motive](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/worldmapbymotive.png?raw=true)


```r
## this takes a minute to load 

tm_shape(world_sf_valid) + 
  tm_borders() +  # Draw borders
  tm_fill(col = "event_type", palette = "Set3", title = "Motive") + # Color by 'Event Type' variable
  tm_layout(main.title = "World Map by Event Type",
            legend.outside = TRUE)

```
![World Map by Motive](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/worldmapbyeventtype.png?raw=true)

```r
# a contingency table for actor_type and event_type
actor_event_table <- table(df$actor_type, df$event_type)

# View the table
print(actor_event_table)

```


```r
# Calculate attack counts per year
yearly_counts <- world_sf_valid %>%
  group_by(year) %>%
  summarise(
    attack_count = n()
  )

# Create the histogram with exact counts
ggplot(yearly_counts, aes(x = year, y = attack_count)) +
  geom_bar(stat = "identity", fill = "steelblue", alpha = 0.7) +
  geom_text(aes(label = attack_count), 
            vjust = -0.5, 
            size = 3) +  # Add count labels above each bar
  theme_minimal() +
  labs(
    title = "Number of Attacks per Year",
    x = "Year",
    y = "Number of Attacks"
  ) +
  theme(
    plot.title = element_text(hjust = 0.5, size = 14),
    axis.text.x = element_text(angle = 45, hjust = 1),  # Rotate x-axis labels
    panel.grid.minor = element_blank()
  ) +
  scale_y_continuous(
    expand = expansion(mult = c(0, 0.1))  # Add some space at the top for labels
  )

ggsave("yearly_attacks_histogram.png", width = 10, height = 6)
```
![Yearly Attacks Histogram](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/yearly_attacks_histogram.png?raw=true)


```r
summary(yearly_counts)
```


```r

# Create a temporary directory for individual frames
dir.create("tmp_frames", showWarnings = FALSE)

create_histogram <- function(data, year, yearly_counts) {
  year_distribution <- data %>%
    filter(year == !!year) %>%
    group_by(actor_type) %>%
    summarise(count = n()) %>%
    ungroup() %>%
    mutate(percentage = count / sum(count) * 100)
  
  ggplot(year_distribution, aes(x = actor_type, y = count, fill = actor_type)) +
    geom_bar(stat = "identity", width = 0.7) +
    scale_fill_brewer(palette = "Set3") +
    theme_minimal() +
    labs(
      title = sprintf("Attack Distribution (%s)", as.character(year)),
      x = "Actor Type",
      y = "Number of Attacks",
      fill = "Actor Type"
    ) +
    theme(
      legend.position = "right",
      plot.title = element_text(hjust = 0.5, size = 12),
      axis.text.x = element_text(angle = 45, hjust = 1),
      panel.grid.major.x = element_blank()
    ) +
    geom_text(
      aes(label = paste0(count, "\n(", round(percentage, 1), "%)")),
      position = position_stack(vjust = -0.1),
      size = 3
    )
}

# Ensure year is numeric in the data and sort
world_sf_valid <- world_sf_valid %>%
  mutate(year = as.numeric(as.character(year))) %>%
  arrange(year)

# Process data for dominant actors
world_sf_valid <- world_sf_valid %>%
  group_by(year, geometry) %>%
  mutate(
    actor_count = n(),
    dominant_actor = actor_type[which.max(table(actor_type))]
  ) %>%
  ungroup()

# Get sorted unique years
years <- sort(unique(world_sf_valid$year))
print(paste("Processing years in order:", paste(years, collapse = ", ")))

combined_frames <- list()
for(i in seq_along(years)) {
  current_year <- years[i]
  print(paste("Processing year:", current_year))
  
  # Create histogram for current year
  histogram <- create_histogram(world_sf_valid, current_year, yearly_counts)
  ggsave(
    sprintf("tmp_frames/histogram_%04d.png", i),
    histogram,
    width = 8,  # Increased width to accommodate bars
    height = 6,
    bg = "white"
  )
  
  # Create map for current year
  current_map <- tm_shape(world_sf_valid %>% filter(year == current_year)) + 
    tm_borders() +  
    tm_fill(
      col = "dominant_actor", 
      palette = "Set3", 
      title = "Dominant Actor Type"
    ) +
    tm_layout(
      legend.outside = TRUE,
      legend.position = c("right", "bottom"),
      title = sprintf("Dominant Actors by Region (%s)", as.character(current_year)),
      title.position = c("center", "top")
    )
  
  tmap_save(
    current_map, 
    filename = sprintf("tmp_frames/map_%04d.png", i),
    width = 8,
    height = 6
  )
  
  map_img <- image_read(sprintf("tmp_frames/map_%04d.png", i))
  hist_img <- image_read(sprintf("tmp_frames/histogram_%04d.png", i))
  
  map_img <- image_scale(map_img, "800x700!")
  hist_img <- image_scale(hist_img, "700x500!")  # Adjusted size for histogram
  
  combined <- image_append(c(map_img, hist_img))
  combined_frames[[i]] <- combined
}

combined_animation <- image_join(combined_frames)
image_write_gif(combined_animation, 
                "attacks_distribution_by_year.gif", 
                delay = 4)
unlink("tmp_frames", recursive = TRUE)
print("GIF creation completed")


## made with help from Claude and 
## https://r.geocompx.org/adv-map#prerequisites-7
## https://r-tmap.github.io/tmap-book/tmshape.html
```


![Attack Distribution by Year](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/attacks_distribution_by_year.gif?raw=true)


## Number of Attacks per Year
The histogram has shown an upward trend in attack counts per year. There will be a significant increase in the number of attacks in 2019-2020 and again in 2021-2022. On both occasions, we saw about a 50% increase in the number of attacks. With a median of 952, it seems as though the number of attacks per year that will be observed in the coming years will increase as well. 

## Attack Distribution by Year
## 2014
Criminal actors (light blue) are widespread, especially in North America, parts of Latin America, and scattered regions in Asia. This indicates that cybercrime was a prominent issue globally, likely involving financial motives. Hacktivists (red) are primarily concentrated in isolated areas, particularly in Europe, showing that politically or socially motivated cyber activities had specific regional impacts.Nation-state actors (dark green) are notably present in East Asia, parts of the Middle East, and North America. This suggests that certain governments were already engaged in cyber operations, possibly for espionage or strategic gains. Undetermined actors (light green) are also present in various regions, indicating that some areas faced cyber activities without clear attribution.
## 2015-2016
The influence of nation-state actors expands, especially across East Asia and parts of the Middle East, pointing to an increase in government-backed cyber operations. This could be related to global geopolitical tensions and a rise in cyber espionage activities. Criminal actors continue to appear frequently across multiple continents, reflecting the steady persistence of financially motivated cybercrime globally.Hacktivist activity remains limited to specific areas but shows sporadic presence, suggesting that hacktivist campaigns may have been reactive to regional social and political events.
## 2017-2018
Nation-state actors maintain a strong presence in Asia and expand to new areas in Europe and North America, which could correlate with heightened global cyber espionage and intelligence-gathering. The dominance of criminal actors remains consistent in many countries, particularly in North America, Latin America, and parts of Europe, indicating an ongoing global problem with cybercrime. Hacktivist activity is sparse but visible in certain hotspots, implying that hacktivism remains active but localized, likely responding to specific issues.
## 2019-2020
Nation-state actor dominance appears to further expand in Asia and parts of Europe, signaling the sustained importance of cyber capabilities in national security strategies. The criminal actor category continues to show prevalence across regions, highlighting the entrenchment of cybercrime in the global threat landscape. Undetermined actors show up in a few regions, which could imply either a lack of attribution or diverse motivations that make categorization difficult.
## 2021-2022
Nation-state dominance becomes more pronounced, especially in strategic regions in Asia, Europe, and North America. This period likely reflects intensifying cyber operations by countries to protect national interests or gain intelligence. Criminal actors maintain a steady global footprint, emphasizing the resilience and adaptability of cybercriminals.Hacktivist activity remains minimal, suggesting that hacktivist movements have become less prominent, possibly due to stronger law enforcement or shifts in social movements.
## 2023 
Nation-state actors continue to dominate many regions, showing how state-sponsored cyber activities have become a central component of international relations and security strategies. Criminal actor prevalence persists, particularly in economically significant or technologically advanced regions, indicating that cybercrime remains a persistent threat worldwide. Undetermined actor presence is observed in certain areas, implying ongoing difficulty in clearly attributing cyber activities in some regions.


## Growth Rates

```r
growth_rate <- yearly_counts %>%
  # Ensure 'year' is numeric
  mutate(year = as.numeric(as.character(year))) %>%
  # Sort by year
  arrange(year) %>%
  # Compute differences and growth rate
  mutate(
    Diff_year = year - lag(year),  # Calculate year difference
    Diff_growth = yearly_counts$attack_count - lag(yearly_counts$attack_count),  # Calculate route difference
    Rate_percent = (Diff_growth / Diff_year) / lag(yearly_counts$attack_count) * 100  # Compute growth rate percentage
  )

## made with help from https://forum.posit.co/t/growth-rate-calculation-in-r/38675/2
## Matthias
## bennyishere

growth_rate

```

```r

# Calculate percent change for each region
world_sf_changes <- world_sf_valid %>%
  group_by(geometry) %>%
  summarise(
    start_count = sum(ifelse(year == 2014, event_count, 0)),
    end_count = sum(ifelse(year == 2022, event_count, 0)),
    .groups = 'drop'
  ) %>%
  mutate(
    percent_change = ifelse(start_count > 0,
                           ((end_count - start_count) / start_count) * 100,
                           ifelse(end_count > 0, 100, 0)),
    # Cap extreme values for better visualization
    percent_change_capped = pmin(pmax(percent_change, -100), 100)
  )

# Create a diverging color palette map
map_changes <- tm_shape(world_sf_changes) +
  tm_borders(col = "darkgray", lwd = 0.5) +
  tm_fill(
    col = "percent_change_capped",
    title = "% Change (2014-2022)",
    style = "pretty",
    palette = "RdYlBu",  # Red-Yellow-Blue diverging palette
    midpoint = 0,        # Center the color scale at 0
    contrast = c(0.2, 1),
    n = 9,              # Number of color classes
    legend.hist = TRUE
  ) +
  tm_layout(
    main.title = "Event Frequency Change by Region (2014-2022)",
    main.title.position = c("center", "top"),
    main.title.size = 1.2,
    legend.outside = TRUE,
    legend.position = c("right", "center"),
    legend.title.size = 0.8,
    frame = FALSE,
    bg.color = "white"
  ) +
  tm_scale_bar(position = c("left", "bottom")) +
  tm_grid(alpha = 0.2)

# Save the map
tmap_save(
  map_changes,
  filename = "event_frequency_changes.png",
  width = 12,
  height = 8,
  bg = "white",
  dpi = 300
)
```


![Event Frequency Changes](https://github.com/darkawesome/blog/blob/main/content/img/Cyber-Analysis/event_frequency_changes.png?raw=true)

# Event Frequency by Region

- North America: Stands out with significant activity, across the entire region.

- Europe: A mix of low-to-moderate activity, with western European countries showing a hgiher percent change in the region. Which could be explained by regional conflicts.

- Asia: Some regions in South Asia and East Asia show moderate to high activity. Which may be attributed to concentrated events in countries like India or China.

- Africa and Oceania: Predominantly low-to-moderate activity in the African region. With few countries seeing moderate-to-high activity. Perhaps being attributted to the number of events in the region not being very high. In Oceania we can see moderate-to-high levels of activity in the region. 

## What's happening?

- Nation-state actors have consistently grown in influence from 2014 to 2023, particularly in Asia, Europe, and North America. This reflects the increasing use of cyber capabilities by governments for both defense and intelligence.

- Criminal actors are widespread across all frames, highlighting that cybercrime is a constant, pervasive issue with global impact.

- Hacktivists appear sporadically, usually concentrated in specific regions, suggesting that their activity is more event-driven and localized.

- Undetermined actors persist in some regions, showing the complexity of attributing cyber activities accurately. This indicates a shift towards a more polarized cyber environment where geopolitical and economic factors drive cyber operations.


### Overall Growth:
From 2014 to 2023, the number of cyberattacks increased significantly. The attack count started at 570 in 2014 and rose to 2,163 in 2023, which reflects a substantial overall increase in cyberattacks over the period.

## Annual Growth Variability:
The growth rate year-on-year is highly variable. For example: The growth from 2014 to 2015 was moderate, at around 23%. The growth from 2015 to 2016 saw a steep rise of 35.8%.

From 2016 to 2017, there was a significant decrease in the number of attacks, with a drop of -27.7%. From 2019 to 2020, there was a sharp spike of 59.3%, possibly reflecting changes in attack patterns, maybe due to the COVID-19 pandemic and the shift to online activities. The largest growth occurred between 2021 and 2022, with a 68.1% increase in attacks, suggesting a notable escalation in cyber activity. Looking at the median the mean can see around a 13% or 9% positive change annually.  However given the amount of fluctuations in the short observable years, not much can be predicted accurately. Although, we shall do it anyway!

## Peak Attack Years:
The peak year for cyberattacks was 2023 with 2,163 attacks, following the rise seen in 2022. The total attack count in these years is higher than the earlier years, however, the database needs to be updated to fully reflect the attacks for that year. 


