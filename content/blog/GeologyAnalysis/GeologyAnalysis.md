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

![https://ibb.co/chCBHRB](https://i.ibb.co/jkWN2qN/RPLTHeme1.png"RPL_Theme1")

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

![https://ibb.co/H4vxnt9](https://i.ibb.co/ZXsYWBb/moransI.png"MoransIPlot")

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
![https://ibb.co/H4vxnt9](https://i.ibb.co/TKNzrXq/morans-Iplot.png")


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

![https://ibb.co/H4vxnt9](https://i.ibb.co/McT8K1s/sovimap.png")

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

![https://ibb.co/H4vxnt9](https://i.ibb.co/jb0M63n/sovitrail.png")

When we map the trails across ages of 18 years and older and 60 and older there is not much difference in the spread compared to where we see the trails neither. There are some hot spots in the 18 and older estimate. Though that could be attributed to the fact that the range of ages is significantly higher than 60 and older. On its head there are no real identifiable spots that stick out in any way in proximity to the trails. On the other hand the clusters seen in the south eastern part of the state for those 18 years old and above does look to be quite darker than other regions. There is a seemingly interesting bend near the middle of the eastern section.Where we see some darker regions following the trails but again we should take scale into account.

```r
##  Persons below 150% poverty (estimate 2016-2020),at the measuring sites
poverty_map <- ggplot() +
  geom_sf(data = tract.sovi.sf, aes(color = E_POV150), size = 3) +
  scale_color_gradient(low = "blue", high = "red", name = "Poverty Below 150% poverty estimate") +
  labs(title = "Poverty Levels at Measuring Sites") +
  theme_minimal()
poverty_map
```

![https://ibb.co/H4vxnt9](https://i.ibb.co/tC0mLjH/povlevel.png")

```r
#map showing Households with no vehicle available (estimate 2016-2020),at the measuring sites
NoVehicle_map <- ggplot() +
  geom_sf(data = tract.sovi.sf, aes(color = E_NOVEH), size = 3) +
  scale_color_gradient(low = "lightblue", high = "darkblue", name = "Household with no Vehicle") +
  labs(title = "Households with no Vehicle at Measuring Sites") +
  theme_minimal()
NoVehicle_map
```

![https://ibb.co/H4vxnt9](https://i.ibb.co/y03RGc9/noV.png")
### Persons Below 150% Poverty Estimate

There seems to be a large cluster around the Philadelphia area. Which again can be due to edge effects for the reasons mentioned above or perhaps more. Additionally, surrounding Philadelphia their seem to be some clustering that is happening across different census tracts with areas west of Lancaster also included. Pittsburgh also is at the center of some clustering. However, when we look on the state as a whole there are few and far between areas of really bad poverty at the state level. At lower levels these would most likely be more pronounced. Especially in areas like Philadelphia being as the hot spots can be seen from the map projection.
### Household with no Vehicle

Looking at the whole state most households have vehicles. Though in the major cities we do see some separation from this. With some parts of the state beginning to enter a more deeper blue color. It would be a safe assumption however to say that for the majority of households withing the state, most have cars. And for that matter, of those who don’t they are near a major city in which they may not need a car for there lifestyle. Given public transportation or even the ability to walk, bike or use a ride sharing app to get to the places they need to go.
## Part 4
### Regression Analysis

```r
# Fit OLS Regression model
model12 <- lm(RPL_THEME1 ~ EP_NOHSDP, data = county.sovi.sf)
summary(model12)
```

#### Call:
#### lm(formula = RPL_THEME1 ~ EP_NOHSDP, data = county.sovi.sf)
#### 
#### Residuals:
####      Min       1Q   Median       3Q      Max 
#### -0.46501 -0.20453 -0.06398  0.22251  0.51435 
#### 
#### Coefficients:
####             Estimate Std. Error t value Pr(>|t|)    
#### (Intercept) -0.13861    0.11205  -1.237    0.221    
#### EP_NOHSDP    0.06620    0.01124   5.890 1.49e-07 ***
#### ---
#### Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#### 
#### Residual standard error: 0.2399 on 65 degrees of freedom
#### Multiple R-squared:  0.348,  Adjusted R-squared:  0.338 
#### F-statistic: 34.69 on 1 and 65 DF,  p-value: 1.495e-07

```r
# Visualize the regression line
ggplot(county.sovi.sf, aes( EP_NOHSDP, RPL_THEME1)) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE, col = "blue") +
  labs(title = "OLS Regression",  EP_NOHSDP = "#Persons with no HS Diploma ", RPL_THEME1 = "Socioeconomic Status theme summary")
```

![https://ibb.co/H4vxnt9](https://i.ibb.co/hyR69vC/olsReg.png")

The coefficient for EP_NOHSDP is 0.06620. This indicates that for a one-unit increase in EP_NOHSDP, RPL_THEME1 is expected to increase by 0.06620 units, if you were to hold the other variables constant.The intercept is -0.13861, representing the estimated value of RPL_THEME1 when EP_NOHSDP is zero.
### Significance of Coefficients:

The t-value for EP_NOHSDP is 5.890, and the corresponding p-value is very small (1.49e-07). This suggests that the coefficient for EP_NOHSDP is statistically significant, indicating that it’s unlikely to be zero.
### R-squared and Adjusted R-squared:

The multiple R-squared is 0.348, meaning that approximately 34.8% of the variability in the dependent variable is explained by the independent variable(s) in the model.The adjusted R-squared (0.338) accounts for the number of predictors in the model and is slightly lower than the multiple R-squared.
### F-statistic:

The F-statistic (34.69) tests the overall significance of the model. With a p-value of 1.495e-07, you can reject the null hypothesis that all coefficients are zero. This suggests that at least one predictor variable is related to the response variable.

In summary, the model suggests that there is a statistically significant relationship between RPL_THEME1 and EP_NOHSDP. The model explains about 34.8% of the variability in RPL_THEME1, and the relationship is significant based on the low p-value from the F-test.

```r 
# Histogram of the Response and Predictor Variable

hist(x=county.sovi.sf$RPL_THEME1)
```

![https://ibb.co/H4vxnt9](https://i.ibb.co/H72vDnF/hist1.png")

```r
hist(x=county.sovi.sf$EP_NOHSDP)
```

![https://ibb.co/H4vxnt9](https://i.ibb.co/XYky8pz/hist2.png")

```r
library(hrbrthemes)

LinearFit <- lm(RPL_THEME1 ~ EP_NOHSDP, data = county.sovi.sf, na.action="na.exclude")

county.sovi.sf$ModelledOutput   <- predict(LinearFit)
county.sovi.sf$Linear.Residuals <- residuals(LinearFit)

# with linear trend
# looking at no hs diploma and socioeconomic status
ggplot(county.sovi.sf, aes(x=EP_NOHSDP, y=RPL_THEME1)) +
  geom_point() +
  geom_smooth(method=lm , color="red", se=FALSE) +
  theme_ipsum()
```

![https://ibb.co/H4vxnt9](https://i.ibb.co/s2qSHDp/lBF.png")

```r
summary(LinearFit)
```

#### 
#### Call:
#### lm(formula = RPL_THEME1 ~ EP_NOHSDP, data = county.sovi.sf, na.action = "na.exclude")
#### 
#### Residuals:
####      Min       1Q   Median       3Q      Max 
#### -0.46501 -0.20453 -0.06398  0.22251  0.51435 
#### 
#### Coefficients:
####             Estimate Std. Error t value Pr(>|t|)    
#### (Intercept) -0.13861    0.11205  -1.237    0.221    
#### EP_NOHSDP    0.06620    0.01124   5.890 1.49e-07 ***
#### ---
#### Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#### 
#### Residual standard error: 0.2399 on 65 degrees of freedom
#### Multiple R-squared:  0.348,  Adjusted R-squared:  0.338 
#### F-statistic: 34.69 on 1 and 65 DF,  p-value: 1.495e-07

The model suggests that there is a statistically significant linear relationship between RPL_THEME1 and EP_NOHSDP. The positive coefficient for EP_NOHSDP indicates that an increase in EP_NOHSDP is associated with an increase in RPL_THEME1.

The R-squared value indicates that the model explains about 34.8% of the variability in RPL_THEME1. This means that other factors not included in the model may contribute to the remaining variability.

The F-statistic is highly significant, supporting the overall fit of the model. In summary, based on the provided information, the model appears to be a statistically significant predictor of RPL_THEME1, and the coefficient for EP_NOHSDP is positive and significant.

# Conclusion

After exploring the spatial characteristics and network analysis of trails in the state of Pennsylvania. The primary objective was to provide valuable insights for Pennsylvania state park rangers and members of the general assembly to enhance tourism, improve trail maintenance efficiency, and deploy AI-driven solutions for recreational infrastructure management.
## Key Findings:
### Spatial Analysis of Trails:

Utilizing spatial analysis techniques, the study examined 2,466 trail data points collected from GPS units and cross-referenced with aerial imagery. A distinct pattern following the Appalachian Trail through the state was observed. K-Means clustering was employed to group trails into 10 clusters based on geographic coordinates. The resulting clusters provided insights into the distribution and dispersion of trails across the state.Network analysis using graph theory was initiated to study the connectivity of trail points. However, due to challenges with the loop structure, further exploration was curtailed.The study integrated data on socioeconomic status, racial and ethnic minority status, education levels, and housing characteristics at the census tract and county levels. The analysis revealed patterns and clusters in these demographic factors across the state.Moran’s I statistic indicated positive spatial autocorrelation, suggesting that similar values were clustered spatially. The analysis hinted at local clusters of similar socioeconomic conditions.Regression analysis was conducted to explore the relationship between socioeconomic status and the number of persons with no high school diploma. The model provided insights into the impact of education levels on socioeconomic status.
### Implications and Recommendations:

The study’s findings can be leveraged to enhance tourism promotion strategies. AI-driven mobile apps can recommend personalized trail experiences based on visitor interests and fitness levels.AI solutions can be deployed to predict and prioritize trail maintenance needs, ensuring efficient allocation of resources and enhancing trail safety.Consideration of demographic patterns and socioeconomic factors can inform community engagement initiatives. Tailored programs can be developed to address the diverse needs and preferences of different regions.
### Further Network Analysis:

Despite challenges in the initial network analysis, further exploration is recommended. Refining the loop structure and addressing errors in the code could provide valuable insights into the connectivity of trail points.Collaborative efforts with environmental scientists, ecologists, and urban planners can enrich the study’s findings. Exploring the relationship between trail networks and environmental factors may contribute to a more comprehensive understanding.

In conclusion, the study presents a multifaceted analysis of Pennsylvania’s trail network, laying the groundwork for informed decision-making in trail management, tourism promotion, and community engagement. Further research and collaboration can build upon these findings for the continued enhancement of Pennsylvania’s recreational infrastructure.

1. https://www.pasda.psu.edu/

    This is where I got the data for the trails from and the specific file name of it is Explore PA trails - Trails (points). This feature class contains points associated with the line feature class for trails in the state of Pennsylvania, as prepared by the PA DCNR, Rails-to-Trails Conservancy, PA Fish and Boat Commission, and Keystone Trails Association. The majority of the data was collected using GPS units and checked for quality and accuracy against high-resolution aerial imagery. See Method. Data was collected between November 2007 and October 2009. See Subtype Code for point type. Trail updates are submitted by users through explorepatrails.com and are evaluated and updated on a regular basis.↩︎

2. Adrian Baddeley, Gopalan Nair, Suman Rakshit, Greg McSwiggan, Tilman M. Davies, Analysing point patterns on networks — A review, Spatial Statistics, Volume 42, 2021, 100435, ISSN 2211-6753, https://doi.org/10.1016/j.spasta.2020.100435. (https://www.sciencedirect.com/science/article/pii/S2211675320300294) Abstract: We review recent research on statistical methods for analysing spatial patterns of points on a network of lines, such as road accident locations along a road network. Due to geometrical complexities, the analysis of such data is extremely challenging, and we describe several common methodological errors. The intrinsic lack of homogeneity in a network militates against the traditional methods of spatial statistics based on stationary processes. Topics include kernel density estimation, relative risk estimation, parametric and non-parametric modelling of intensity, second-order analysis using the K-function and pair correlation function, and point process model construction. An important message is that the choice of distance metric on the network is pivotal in the theoretical development and in the analysis of real data. Challenges for statistical computation are discussed and open-source software is provided. Keywords: Distance metric; Kernel density estimation; K-function; Nonparametric estimation; Pair correlation function; Stationary process↩︎

