---
title: "Nuclear Weapons Analysis"

date: 2022-12-24T14:11:48-04:00
url: /NuclearWeapons/
image: /img/dataAnalysis.jpg
categories:
  - Data Science
tags:
  - Blog
draft: False
---
This is my data analyis of the effects of human rights abuses on NPT party members on the respective countries nuclear arsenal.
<!--more-->

## The Why 
Hypothesis: What are the effects of the number of nuclear weapons on human rights abuses?

Theory : Having nuclear weapons in the country acts as a force to keep the country in check. However, to be honest I think it is more of having more nuclear weapons would result in fewer cases of abuse. Why that happens I think is that having nuclear technology presupposes that a nation has a centrifuge, a nuclear reactor and other facilities that aid in building up of nuclear weapons. And my thinking is that if a nation has all of those things they can't also be killing random people as they could kill the head of the nuclear weapons program. 

[![The Dictator (2012) - Nuclear Nadal Scene](https://i.ytimg.com/vi/OMDQzITWJyU/maxresdefault.jpg)](https://www.youtube.com/watch?v=vV30irsal-w)

**Does the number of nuclear weapons have an effect on human rights abuses?**

Nucleaar weapon count over years
![ImageOne](https://gcdnb.pbrd.co/images/EWpfEGzcdSZa.png?o=1) 

For the dependent variable “Nukes” the mean is 5213.4 while the median is 235. Looking at this we can use the median as the true center. As it is not skewed by the outliers in the data. The standard deviation of the “Nukes” variable is 10022.22 and this high number makes sense when we account for outliers and high numbers. This means that a datum would be very far from the mean on average. Looking at the interquartile range we can confirm the above assumptions made about the data. The first quartile is 34.5 and the third is 2414.5 with a min and max of zero and 40159 respectively. When we look at the association between Nukes and Women’s Political Rights we find a Pearson's R of 0.313999 we have a weak yet positive linear correlation. When fitting a linear model to “Nukes” and “Women’s Political Rights” we find an R-squared value of 0.0986 to say that this line explains only 9% of the data in the full data table. Moreover, we find that “Women’s Political Rights” is a statistically significant variable that has a p-value of 9.2e -06. The  𝛽 variable (Nukes) has a value of 7478, which means for every single unit increase in x (Women’s Political Rights ) on average the number of nukes will increase by 7478. 

Nuke count vs. Freedom of Assembly and Association
![ImageTwo](https://gcdnb.pbrd.co/images/362xN2KexmQd.png?o=1) 

The independent variable “Freedom of Assembly and Association” had a median value of
1 and a mean of 1.292. What this means when looking at the coding for this variable is that on
average most countries had practiced occasional killing of people (from 1 to 49 killings) due to
governmental limitations or restrictions on freedom of assembly and association. Looking at the
data on a graph we can see that as the number of nukes increases there is actually an increase that
happens in the number of killings. Most of the data that is associated with killings not occurring
or not being reported has happened between 25,000 and 10,000 nukes. The variance of this
variable is 0.5951134. To mean that most of the data is pretty varied given it's only from zero to
two. The chi-square statistic here is quite high (299.72)and confirms that our data is quite varied.
Looking at the correlation with a hypothesis test we find that though the correlation is not 0. It is
low enough I would say that it is essentially 0 (-0.01900094).

Nuke Count vs. Torture
![ImageThree](https://gcdnb.pbrd.co/images/uh3OAZzy5mdO.png?o=1) 

The independent variable “Torture“measures anything from simple beatings to other
practices to get a forced confession or information. The mean of this is 0.5625 and the median is
1. Now when we plot the data we can see that it is skewed toward zero and most of the data is
spread across zero and one. Using the coding book, this means that most of the countries have
practiced torture frequently (50 or more) or practice it occasionally respectively (1 to 49). Now
looking at the variation we have a value of 0.4986911. Now the correlation coefficient is
0.09632143 which shows a relationship that is not very strong nor is the association very strong.
Again here we have a variable with a large chi-squared value (278.31). So all in all it seems that
this variable doesn’t account for too much of the variation that we see in the dependent variable.
After running a bivariate regression on these variables we can that this assumption is confirmed
with an R-squared value of 0.009278, To mean, that torture is responsible for 0.9% of the
variation we see in Nukes.

Nuke Count vs. Disappearance
![ImageFours](https://gcdnb.pbrd.co/images/X1WrLwL9Kwog.png?o=1) 

The independent variable “Disappearance” is based on cases of people disappearing.
Plotting the data out to get a good view of it shows a lot to look at. And it seems as though the
majority of the data is split across the left and right poles. Where it is a zero, meaning 50 or more
disappearances or it is a two which means no disappearances or they have not been reported. The
median value is two and the mean is 1.594. The variance here is 0.4937827 however from what
we have seen in the other variances this inconclusive. The correlation between Disappearance
and Nukes is -0.02468511. Running a bivariate regression with Nukes we see that
Disappearances account for basically zero percent of the variation we see in Nukes ( 0.0006094).

![Presentation]()

## Conclusion

Looking at the Results of the models we have found that only Women's Political Rights and Extrajudicial Killings have any effect on Number of Nukes a country has. However, what these models fail to account for are the domestic politics that are involved. Though these findings make sense looking at the models and the modeling they have a low external validity. 
+ Another thought to consider is the number of zeros being so high that it makes certain terms not 
+  Looking at Tortures effecton Nukes the chi-squared value (278.31). So all in all it seems that this variable doesn’t account for too much of the variation that we see in the dependent variable.

