---
title: "EveOnlinePI"

date: 2024-12-05T15:48:30-05:00
url: /EveOnlinePI/
image: /images/2020-thumbs/EveOnlinePI.jpg
categories:
  - Linux
  - Windows
  - Networking
tags:
  - Ubuntu
draft: true
---
<!--more-->



# Planetary Commodities Analysis in EVE Online

## Introduction

EVE Online's intricate economy is a fascinating playground for strategists, traders, and industrialists. At its heart lies Planetary Interaction (PI), a cornerstone of the game's resource production. With its ties to production chains and interstellar logistics, PI commodities represent lucrative opportunities for those who understand the market dynamics.

This blog post explores how to harness Google Sheets and the EVE ESI API for analyzing planetary commodity prices, optimizing trade routes, and identifying market trends. We'll focus on key market hubs like **Jita**, **Amarr**, and **Dodixie**, leveraging tools and data to make informed trading decisions.

For full disclosure, I did this project on behalf of an alliance mate for in-game currency.

![PI CookBook](https://github.com/darkawesome/blog/blob/main/content/img/EveOnline/PICookbook.png?raw=TRUE)

Originally from dama.arishe@gmail.com but updated for the ESI api



## 1. Data Collection

### Understanding the EVE ESI API

The EVE Swagger Interface (ESI) is CCP's official API, providing a wealth of data about EVE's universe. For this analysis, key endpoints include:
- **Market Orders**: Regional buy and sell orders.
- **Type IDs**: Identifiers for planetary commodities.
- **Market History (Optional)**: Price trends over time.

### Google Sheets Integration

I used Google Apps Script to fetch live data from the ESI API, enabling dynamic updates of market data. Using the [GESI](https://github.com/Blacksmoke16/GESI) add-on, which provides an EVE Online SSO flow to authorize your character(s), as well as a wrapper around ESI in order to access your EVE Online data within Google Sheets™; much like `=importXML()` worked with the old XML API.

## 2. Market Hubs in EVE Online

### Key Trade Centers
- **Jita 4-4**: The universal trade hub, with unparalleled liquidity and item availability.
- **Amarr**: The second-largest hub, catering to empire-wide trade.
- **Dodixie, Hek, Rens**: Smaller hubs that offer niche trading opportunities.

![Market Orders](ihttps://github.com/darkawesome/blog/blob/main/content/img/EveOnline/MarketOrders.png?raw=TRUE)
By : foo-eve.blogspot.com.au
foobar.downunder@gmail.com but updated to pull from the ESI API


### Regional Variations

Prices fluctuate based on regional production and demand. Mining-heavy regions may see lower buy prices, while mission-running hubs often feature higher sell prices.


## 3. Google Sheets for Market Analysis

```javascript

* Custom function to fetch the average price for an item by its TypeID using GESI's markets_prices function.
* @param {number} typeID The TypeID of the item you want to look up.
* @return {string} The average price of the item, or an error message if not found.
* @customfunction
*/
function getAveragePrice(typeID) {
 try {
   // Fetch market prices using the markets_prices function for the given TypeID
   var result = GESI.invoke("markets_prices", { type_id: typeID });
  
   // Check if result data is available
   if (result && result.length > 0) {
     // Iterate through the result to find the matching TypeID and get the average price
     for (var i = 0; i < result.length; i++) {
       // The TypeID is in the third column (index 2)
       if (result[i][2] == typeID) {
         var averagePrice = result[i][1];  // The average price is in the second column (index 1)
         return averagePrice ? averagePrice : "Price not found";  // Return average price or "Price not found"
       }
     }
     return "TypeID not found in market data";  // Return if no matching TypeID is found
   } else {
     return "No market data available";  // Return if no data is available
   }
 } catch (e) {
   // Handle errors (e.g., invalid TypeID or network issues)
   return "Error: " + e.message;
 }
}


/**
* Returns the minSell and MaxBuy price for the provided typeId at the provided locationID
*
* @param {number} typeId to get privve data for
* @param {number} locationID to filter on. Can either be a structre or a station ID.
* @param {number} regionID to filter on. Only required if locationId is a station ID.
*
* @customfunction
*/
```



```javascript
/**
* Fetches minimum sell price and maximum buy price for a given item and location.
* @param {number} typeID - The TypeID of the item.
* @param {number} locationID - The location ID to filter orders (structure or station).
* @param {number} regionID - The region ID to fetch orders from if location is not a structure.
* @return {Array} [minSell, MaxBuy] - Minimum sell price and maximum buy price.
* https://www.youtube.com/watch?v=sRiLmEhsWW0
* Flying Scorpion
*/
function typePriceData(typeID, locationID, regionID) {
 // Fetch market data
 var data = locationID > 10000000000000
   ? GESI.invokeRaw("markets_structure_structure", { structure_id: locationID })
   : GESI.invokeRaw("markets_region_orders", { region_id: regionID, order_type: "all", type_id: typeID });


 // Initialize prices
 var minSell = null;
 var maxBuy = null;


 // Process each order
 data.forEach(function(order) {
   if (order.type_id === typeID) {  // Match the TypeID
     if (order.is_buy_order) { // Buy order
       if (!maxBuy || order.price > maxBuy) {
         maxBuy = order.price; // Update max buy price
       }
     } else { // Sell order
       if (!minSell || order.price < minSell) {
         minSell = order.price; // Update min sell price
       }
     }
   }
 });


 // Return results
 return [[maxBuy, minSell]];
}

```
## 4. Major Problems

I never ended up finding any real nomenclature here. A lot of testing occurred with the commands that I would find when testing markets. However, my saving grace and what led to my eventual success was a mixture of the [esi-docs](https://github.com/esi/esi-docs), [Eve Reference docs](https://docs.everef.net/datasets/market-history.html), and using [Eve Tycoon](https://evetycoon.com/market/2870). Whether it was using the URL string to find the regionID or looking for the typeID, all of these sources helped in my eventual completion of this project.

## 5. Trade Route Analysis

### Hek to Rens

Secondary trade hubs like **Hek** and **Rens** serve as critical regional marketplaces. While **Jita** and **Amarr** dominate intergalactic trade, **Hek** and **Rens** offer niche opportunities for arbitrage and market specialization.

Although a bit out-of-the-scope of this project, I do regularly use this tool to see if I am making a solid profit from selling my planetary materials. I do so by looking at the graph and the current market conditions. Things like a high number of sell orders tend to cut into my profits, as price gouging and competition with players who have more volume than me affect my assets greater. However, when I look at spreading my assets to other regional markets like **Dodixie** or **Rens**, I tend to get a better net average return than staying in one trade market.

### Hek and Rens: Niche Markets and Regional Insights

#### Hek: The Minmatar Industrial Center

Located in the **Metropolis** region, **Hek** serves as a convenient trade hub for industrialists and miners.

**Specialized Commodities:**
- **Robotics**: Commonly used in advanced manufacturing, **Robotics** are in demand due to **Hek's** proximity to mining-heavy areas. This demand often leads to higher prices compared to **Jita** or **Amarr**.
- **Construction Blocks**: With local industry focusing on ship and structure production, **Construction Blocks** tend to perform well here.

#### Rens: The Minmatar Trade Gateway

Situated in the **Heimatar** region, **Rens** acts as a gateway for traders servicing newer players and **Minmatar** space.

**Specialized Commodities:**
- **Coolant**: This commodity frequently sees demand due to the prevalence of planetary interaction in the region.
- **Viral Agent**: A critical component for advanced industry, **Viral Agents** see price fluctuations in **Rens**, making it a lucrative item for arbitrage from **Jita** or **Amarr**.

### Logistics Tip:

**Rens** often suffers from stock shortages compared to larger hubs, meaning items like **Nanostructures** and **Plasmoids** can fetch a premium when delivered in bulk.

## 6. Closing Thoughts

This was a pretty fun project that I did for an in-game alliance mate for in-game currency, although it has a wealth of applications. The scalability of it is also very high, as you would just need to add more items to the table or add more systems. The same could be done with the P4s in building a greater amount of planetary interaction chains.



