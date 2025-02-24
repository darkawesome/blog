---
title: "PriceQuote"

date: 2025-02-24T10:24:03-05:00
url: /PriceQuote/
image: /images/2020-thumbs/PriceQuote.jpg
categories:
  - Linux
  - Windows
  - Networking

draft: false
---
This is a continuation of my API learning from the previous EVE Online project. This time I was making a spreadsheet to aid a person within my alliance that would calculate the price to produce certain ships.
<!--more-->
This is a continuation of my API learning from the previous EVE Online project. This time I was making a spreadsheet to aid a person within my alliance that would calculate the price to produce certain ships. I had a bit of restrictions that made the creation more tricky than previously where I had a template. For this, I had to meet the following criteria :

- It had to be easy to use Require no coding on their part
- And just be them inputting a few things and getting the prices
- Has to include  a 1 stop shop for the information if building multiple
- Similar to the format that is outputted in Ravworks
- Separate sheets for each ship

This added a bit of complexity to the project as there are a lot of considerations that go into the pricing and determine how accurate the pricing will be. Things to consider would be the skills of the account that would lower manufacturing costs and what station it was being built at that also could lower the manufacturing costs. Within the game, you use blueprints to build the ships and you can upgrade said blueprint to reduce the time to build the ship. Or you can reduce the materials needed to build the specific ship.

I started by importing the information that I got from Ravworks on a per-sheet basis to split everything up. However, I soon learned later that it would be better to sort the data in Ravworks before importing it to my Excel sheet. It complicated the master table in a way that I was looking for the specific line the item was on my master table. Since the items used across these ships cross-pollinated a good bit I moved to make a master list of all the items. The problem mentioned above that arose from this was that when I did my first sheet and went to test it out it was fine.  What I didn’t know was that when I made a second sheet and went to add the information to the master sheet it would be a nightmare.

=sum('Phoenix Navy'!C6+'Revelation navy '!C5+Zirnitra!C5+Phoenix!I102+Phoenix!C6+Revelation!C5+'Naglfar Fleet Issue'!C5+Thanatos!C5+Nidhoggur!C5+Lif!C5)

This was what a call looked like from the master table…. And for some reason instead of looking for a command or making one that parsed over the list to find a better solution…. I manually entered them for the first two ships. Understandable it wasn’t all that bad to do. However, at scale, this was the worst way of going about the problem. That's when I sorted the names of the items to make my life a bit easier. However, there was still a bit of going back and forth for other commands within the project.

Thankfully, I was showing this to someone in my group within the game that explained that vlookup would be perfect for this use case and I implemented it immediately. I understand this command is probably handled in most classes or even a basic Excel book. However, I am self-taught and I just figure it all out as I go.

=VLOOKUP(A6,'Master Table'!$B$2:$D$302,3,TRUE)*C6

This was the command that I started implementing to 10x my process and made the implementation of a new ships materials take about 3 or 5 minutes depending on if I got the colors right the first time or not.

![Phoenix_Navy](
https://github.com/darkawesome/blog/blob/main/content/img/PriceQuote/For%20Sky%20Capitals%20-%20Phoenix%20Navy_page-0001.jpg?raw=true)

![Phoenix_Navy](https://github.com/darkawesome/blog/blob/main/content/img/PriceQuote/For%20Sky%20Capitals%20-%20Phoenix%20Navy_page-0004.jpg?raw=true)

The above images show approximately what one ship's worth of information looks like to implement. Given a change to the highlighted sections, you can figure out how much will be needed for each material and what the price would be given the largest market in the game.

The master table looks like the following and mainly is there to show what the sum of the needed materials would be. The thought process was to make it so a single sheet could be used on its own or if you want to look at the totals the master sheet shows the same information.

![Master_table](https://github.com/darkawesome/blog/blob/main/content/img/PriceQuote/For%20Sky%20Capitals%20-%20Master%20Table_page-0001.jpg?raw=true)

I did leave some notes in the margins to make calling the API easier. I guess for something professional this would be a faux pas but I think it's fine for this.

## Conclusion

For this particular project, one of the biggest things that I learned was that when dealing with someone who has an idea of what they want in their head you have to ask a lot of questions upfront. I thought I had done that however, I needed to ask a lot more. These extra considerations were not talked about nor did I know what her process was in the past, or if she had been buying the materials to build the ships or doing other activities to get them. I still had fun with this project as it taught me a lot about the Vlookup command and how to better integrate an API with Excel to get faster performance.

One of the biggest things I didn’t go over with her was her production process that I could add so she didn’t lose money. For a part of it she would ship the items in and for that import there are other costs associated besides just the item price. Even so besides the manufacturing costs that are complex to calculate this sheet acts as a simple quote checker that someone could use and add their manufacturing costs to get a more accurate picture of what the costs will be.
