---
title: "PLSC421 Final"

date: 2023-10-09T11:44:39-04:00
url: /PLSC421-Final/
image: /images/2020-thumbs/PLSC421-Final.jpg
categories:
  - Data Science
draft: False
---

Report on Foreign Aids effect on African Countries
<!--more-->


# PLSC 421: Data Analysis Report 
## Corruption in the Sub-Sahara 
### I. Introduction
In the past decade has foreign aid fostered corruption in African Countries? The answer to this question is one that can lead to major changes in lots of countries' inflows. As well as a change in how funds are appropriated in a country from years past. If a country receives enough aid from other countries then they do not need to spend any resources fixing the corruption in the country. For instance, if food and water are being covered by aid, then the money that would’ve been used to invest in the country can line politicians' pockets.

The Independent variable would be Corruption Perceptions Index and my dependent variables would be the year, GDP, aid, and human rights abuses. I will include these to see if the corruption changes as the aid increases or decreases. As well as looking at the human rights abuses as I believe that they will start to increase as the aid continues. I will use the world bank open data to get most of my information and the Penn State Library online resource. I will be using regression models to analyze the data and hypothesis tests to see if there is a difference between the countries of different regions in Africa.

### II. Literature Summary

“On the effect of foreign aid on corruption” is a paper that found evidence to negate the findings of Okada and Samreth who propose that aid deters corruption. As for the case of African countries that were developing, aid was actually fuelling corruption across the continent.
In “The Effect of Corruption and Governance on Foreign Aid and Global Competitiveness in West Africa”, the purpose of the study was to show how different African countries were when it comes down to corruption, governance, foreign aid, and Global competitiveness. This study follows a claim that corruption was an effect of some countries in Africa following the examples of other corrupt officials in other countries in their efforts to enrich themselves. 

In “Foreign Aid and Corruption: Clarifying Murky Empirical Conclusions” the authors make the case that based on previous work in the area there is no evidence to suggest that foreign aid reduces corruption. First,  it may not be easily verified by donors in terms of the input requirements for a particular output. Therefore, aid funds get lost due to the attitudes of the aid recipient governments in efficiently disbursing funds. This is unlike other forms of aid that are directed at sectors like infrastructure and program-related funds as you can see how the funds are used from the products... Second, improving the external economies of scale for foreign investment inflow through infrastructural development and ensuring investment security, is a central national policy of developing countries. They concluded that aid donors should carefully consider the aid flows that have measurable outcomes or are easy to evaluate. And that aid in the form of social services should be reevaluated as they have been prone to increase corrupt practices.

### III. Theory & Hypothesis

I believe that when it comes to African countries those in the literature have stated, Okada and Samreth’s conclusion doesn’t apply. I hypothesize that foreign aid contributes to corruption because if a country receives enough aid they do not need to spend any resources fixing the corruption in the country.

### IV. Data Analysis

### Figure 1 

Looking at transparency, accountability, and corruption in the public sector rating, we can see a high incidence of missingness as well as most countries in Sub-Saharan Africa receiving a three for their rating. On a scale of 1 to 6 with 6 being high levels this is concerning. Since the levels observed are in the middle of the scale I chose to leave the missing data rather than skew it to a side.

==================================================================================================
                                                    Dependent variable:                           
                          ------------------------------------------------------------------------
                          transparency, accountability, and corruption in the public sector rating
--------------------------------------------------------------------------------------------------
GDP per capita                                             0.0003                                 
                                                                                                  
Foreign Direct Investment                                  -1.949                                 
                                                                                                  
Human resource Rating                                      0.374                                 
year                                                                                              
                                                                                                  
Constant                                                   2.891                                  
                                                                                                  
--------------------------------------------------------------------------------------------------
Observations                                                332                                   
R2                                                         1.000                                  
==================================================================================================
Note:                                                                  *p<0.1; **p<0.05; ***p<0.01


After running the regression models what we see here is that aid does in fact negatively affect corruption CPIDA rating in Sub-Saharan African Countries. Also when we take into account aid and human resource rating these effects are less impactful than what was estimated. And we can agree with the literature on this topic to say that Okada and Samreth got it wrong. Aid does not help developing countries and in fact, it plays a role in hurting them by means of corruption and almost even their GDP. The fixed effects model, as well as the random effects models both, also show similar results to the findings of a pooled OLS regression.

### Conclusion

All in all it would seem as though trying to do the right thing for developing countries actually plays a role in them continuing to be corrupt. The aid that goes to help a country develop and become better on the global stage gets misused and funds go missing. I would agree with the authors of Foreign Aid and Corruption: Clarifying Murky Empirical Conclusions. To say that it is probably happening as when it comes to money for social service causes like education and so forth the funds get appropriated. While measurable things like building infrastructure and other types of aid that can be measured are not misappropriated as they have a type of check on them. If there is no building then why would a donor continue to give money to a nation that doesn’t use the funds as intended? 

### Bibliography

Simplice A, Asongu. “On the Effect of Foreign Aid on Corruption.” SSRN Electronic Journal, Feb. 2012, https://doi.org/10.2139/ssrn.2493289.

Uchenna, Efobi. et al. “Foreign Aid and Corruption: Clarifying Murky Empirical Conclusions.” Foreign Trade Review, vol. 54, no. 3, 2019, pp. 253–263., https://doi.org/10.1177/0015732519851633.

Kenneth, Klutse. “The Effect of Corruption and Governance on Foreign Aid and Global Competitiveness in West Africa.” The Chicago School of Professional Psychology, Feb. 2020, pp. 1–253.

Herbert H, Werlin.“Corruption and Foreign Aid in Africa.” Orbis, vol. 49, no. 3, June 2005, pp. 517–527., https://doi.org/10.1016/j.orbis.2005.04.009.









