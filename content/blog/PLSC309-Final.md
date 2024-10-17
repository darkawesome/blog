---
title: "PLSC309 Final"
url: /PLSC309-Final/
image: /images/2020-thumbs/PLSC309-Final.jpg
categories:
  - Data Science
draft: False
---

What are the effects of the number of nuclear weapons on human rights abuses?
<!--more-->
This is a paper that I did in the Fall of 2022

# PLSC 309: Data Analysis Report 
## Huamn rights Abuses 
### Literature Review
#### Overview
  If a country has a high number of nuclear weapons then this will result in fewer cases of Human rights abuses
Nuclear Proliferation and Human Rights Abuses 
For my research, I plan to look at the effects that a countries nuclear arsenal has on its human rights abusses. Although the connection between the two may not be very obvious at face value. It is my belief that the larger a countries nuclear arsenal the less abuses it will have 
The only problem I was faced with when asking this question was finding information to back this claim as accurate numbers on a countries nuclear arsenal for countries like North Korea are only estimations. And so I decided to not use these countries in my research and chose only to focus on those that had Nuclear weapons at the signing on the Treaty on the Non-Proliferation of Nuclear Weapons (NPT).

  As of this point there are some articles that have to do with human rights and Nations arms and nuclear stockpiles but from my research I have been able to find anyone asking the real questions. However, I did get one account that was close. In “Arms Control and Human Rights” by Kenneth L. Adelman he gives his first hand account of being from the Soviet Union and the Human Rights abuses that the country puts the people through.  In the end he makes a statement to say that we in western democracies are the ones to “deter totalitarian aggression..” When i read this I began thinking, perhaps, nuclear weapons detering human rights abuses have a conection to the nations regime. Perhaps it is being a democracy that lessens the human rights abuses. However, I didn’t think that was all too true given that the location of the soviet union being so far and china being close to them and also having nukes. I will include the location of the countries as I do believe it will yield important information when looking at abuses.

  In “Nuclear Disarmament : The Interplay Between Political Commitments and Legal Obligations” by Dieter Fleck, he attempts to  find the connection that exists in politics to lead to proliferation and disarmament. One of the important things discussed was how the threat of nuclear war has passed and countries can now start to disarm. However, disarming has a plethora of effects that can happen as a result of it. For instance, the country disarming can be seen as weak or it can push a country to make more as their enemy has less. What I think is interesting however in the end when he address the complexity involved here he begins to say that the countries that aren’t in NPT also have a foothold in the conversation when it comes to the decision to have nuclear weapoons or to disarm. 

  Looking more at outside influences that would influence action from NPT signing countries the European Union has made headway towards punishing bad actors that don’t follow the NPT or haven’t signed it. In “The EU’s Evolving Response to Nuclear Proliferation Crises : from Incentives to Sanctions” , he defines the way in which he examines the “mobilization of instruments by the EU as “...situations where astate undertakes actions that are regarded by others acontravening the non-proliferation regime.” (PORTELA 2). And this again brings a level of complexity that I did not think about initally but think I will include in my work. 

  In the last article I will be looking at it furthers the idea of external forces impacting changes on countries. However this one primarily focuses on the avenues being used to address proliferation. Daniel Verdier, examined how countries can form sort of an alliance with a mix of threats and bribes. And the degree of each would be determined by whterher or not they comply to the terms and what incentives they would receive. And he uses NPT membership data to support his claims. With these ideas I thought including the country or part of the world is important to show in my findings. 

### Works Cited
ADELMAN, KENNETH L. “Arms Control and Human Rights.” World Affairs, vol. 149, no. 3, 1986, pp. 157–62. JSTOR, http://www.jstor.org/stable/20672104. Accessed 21 Sep. 2022.

FLECK, DIETER. “NUCLEAR DISARMAMENT: THE INTERPLAY BETWEEN POLITICAL COMMITMENTS AND LEGAL OBLIGATIONS.” New Perspectives, vol. 26, no. 1, 2018, pp. 56–62. JSTOR, https://www.jstor.org/stable/26497635. Accessed 21 Sep. 2022.

PORTELA, Clara, "The EU's Evolving Response to Nuclear Proliferation Crises: from Incentives to Sanctions" (2015). Research Collection School of Social Sciences. Paper 1687.https://ink.library.smu.edu.sg/soss_research/1687
Verdier, Daniel. “Multilateralism, Bilateralism, and Exclusion in the Nuclear Proliferation Regime.” International Organization, vol. 62, no. 3, 2008, pp. 439–476., doi:10.1017/S0020818308080156.

### Data Analysis 
Theory : Having more nuclear weapons would result in fewer cases of abuse as the country with fewer
will focus on internal disputes rather than international ones as they can't compete.

```r
### setwd("/home/banana/Documents/Fall 22 classes/PLSC309")

library(tidyr)
library(tidyverse)
library(dplyr)
```
```r
nuclearData <- read.csv("Nuclearwarheads.csv") 

rightsData <-  read.csv("database.csv")
```
```r
str(nuclearData)
head(rightsData)
str(rightsData)
### data is ugly and needs cleaned
```

```r
cNuclearData <- nuclearData[-c(1 :36), ] 
cNuclearData <- nuclearData[-c(68 :78), ] 
head(cNuclearData) ### now the years for observation start at 1981
```
```r
cRightsData <- rightsData[order(rightsData$Year),] 
head(cRightsData) ### data is sorted by year instead of whatever it was before 
tmp <- 
cRightsData %>%
  filter(Country == "Israel")
tmp1 <- 
  cRightsData %>%
  filter(Country == "France")
tmp2 <- 
  cRightsData %>%
  filter(Country == "United States of America") 
tmp3 <- 
  cRightsData %>%
  filter(Country == "United Kingdom") 
tmp4 <- 
  cRightsData %>%
  filter(Country == "Russia") 
tmp5 <- 
  cRightsData %>%
  filter(Country == "Soviet Union") 
tmp6 <- 
  cRightsData %>%
  filter(Country == "China") 
tmp7 <- 
  cRightsData %>%
  filter(Country == "India") 
tmp8 <- 
  cRightsData %>%
  filter(Country == "Pakistan") 
  ## there is probably a better way to do this but im going to do a left join and call it a day 
  ## for copy and paste purposes ("United States of America" ,"United Kingdom " , "France", "Russia", "Isreal","India", "Pakistan", "Soviet Union", "China")
## Need to figure this out  *** I figured it out *** 

```

```r
FinRightsData <-
  tmp %>%
  full_join(tmp1) 

FinRightsData1 <-
  tmp2 %>%
  full_join(tmp3)

FinRightsData2 <-
  tmp4 %>% 
  full_join(tmp5)

FinRightsData3 <-
  tmp6 %>%
  full_join(tmp7)

## so far, all we did was join 2 tables like france and the US. idk if those were joined but thats an example
## their most definetly is an easier way to do it but at this time this is what we are doing
```

```r
tmpFin <-
  FinRightsData %>% 
  full_join(tmp8)

tmpFin1 <-
  FinRightsData1 %>%
  full_join(FinRightsData2)

tmpFin2 <-
  FinRightsData3 %>%
  full_join(tmpFin)

RightsDataClean <-
  tmpFin2 %>%
  full_join(tmpFin1)  ### All 9 obs are in this guy. And the data is cleaned and wrangled 
  
## Could change soviet union to russia but not necessary. Given this is a historical differentiation
## Also we will strip N. Korea from this as we cant really trust the numbers and they would be approximations 
```

```r
NuclearDataClean <- subset(cNuclearData, select = -c(North.Korea) )
RightsDataClean <- subset(RightsDataClean, select = -c(UN.Country.ID,UN.Region,UN.Subregion))

RightsDataCleaner <- RightsDataClean[order(RightsDataClean$Year),] 
```
```r
###Actual data work starts here 

head(NuclearDataClean)
head(RightsDataCleaner)

RightsDataCleanery <- na.omit(RightsDataCleaner) ###  rights data has soviet union and russia
```
```r
### we can make a pre and post cold war column or make a soviet union and russia column and decide how to proceed from there
### going to join the data and add a 1 or 0 to signify the pre or post cold war
## I joined the two tables by hand as the formatting would've taken longer to fix 
fullData <-  read.csv("Joined.csv")
summary(fullData)

##fullData$logNukes<-log(fullData$Nukes)
```
```r
#######################
#### DATA ANALYSIS ####
#######################

model_WomensRights <- lm(formula = Nukes ~  Women.s.Political.Rights + Women.s.Social.Rights +Women.s.Economic.Rights, data = fullData)
summary(model_WomensRights)

install.packages("stargazer")
library(stargazer)
stargazer(model_WomensRights, type = "text")

fulljawnski <- lm(formula = Nukes ~ Disappearance+Torture+Freedom.of.Speech+Women.s.Political.Rights+Extrajudicial.Killing+Freedom.of.Religion..New.,data = fullData)
summary(fulljawnski)
stargazer(fulljawnski,type = "text")


plot(fullData$Women.s.Political.Rights, fullData$Nukes)
```

