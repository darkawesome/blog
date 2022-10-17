---
title: "Quote"

date: 2021-09-07T23:46:57-04:00
image: /images/2020-thumbs/contact.jpg
categories:
- Linux

draft: false
---
"Be yourself; everyone else is already taken." -Oscar Wilde

<!--more-->

In this project I set up a script to run every so often to keep me focused on my end goal. When it runs this quote to shows up on my command line to remind myself to be myself. And to do the work that I want to do in my free time rather than just waste time on Youtube or Netflix. The grind is now what keeps me working and motivated as its the only way to improve.

Below is the code for the script:
```bash
	#!/bin/bash

  `webpage=$(curl -s 'https://www.goodreads.com/quotes'   -H 'Connection: keep-alive'   -H 'Cache-Control: max-age=0'   -H 'DNT: 1'   -H 'Upgrade-Insecure-Requests: 1'   -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159Safari/537.36' | pup "body > div.content > div.mainContentContainer > div.mainContent > div.mainContentFloat > div.leftContainer > div.quotes > div:nth-child(1) > div.quoteDetails > div.quoteText text{}")`

 echo $webpage
 ```

And to make it show on my command line I used fcron and set up a cron job. This will run the script every 30 minutes, every hour, Monday through Friday, nine to five. As those are the times I'm usually on my linux machine.  

Below is the code for the cron job :

```bash
*/30 9-17 * * 1-5 quote
```


The main purpose of this was to use pup to get a specific css selector and learn from there. PUP is a command line tool for processing HTML. It reads from stdin, prints to stdout, and allows the user to filter parts of the page using css selectors. (https://github.com/EricChiang/pup)

I have recently been learning about the Beautiful Soup and Selenium libraries and I will be making a Amazon Scrapper for a the Aionian Bible Project to implement on their website (http://www.dgjc.org/aionian-bible). That post should come after this one unless another project takes priority.
