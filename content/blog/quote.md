---
title: "Quote"

date: 2021-09-07T23:46:57-04:00
image: /images/2020-thumbs/contact.jpg
categories:
  - Shell Script
  - Data Science

draft: true
---

I made this simple script to show up on my command line and remind myself to be myself. And to do the work that I want to do in my free time rather than just waste time on Youtube or Netflix. 

Below is the code for the script: 
```bash
	#!/bin/bash

  `webpage=$(curl -s 'https://www.goodreads.com/quotes'   -H 'Connection: keep-alive'   -H 'Cache-Control: max-age=0'   -H 'DNT: 1'   -H 'Upgrade-Insecure-Requests: 1'   -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159Safari/537.36' | pup "body > div.content > div.mainContentContainer > div.mainContent > div.mainContentFloat > div.leftContainer > div.quotes > div:nth-child(1) > div.quoteDetails > div.quoteText text{}")`

 echo $webpage
 ```

And to make it show on my command line I used fcron and set up a cron job 

///Below is the code for the cron job : 

```bash
*/30 9-17 * * 1-5 quote
```


The main purpose of this was to use pup to get a specific css selector and learn from there. PUP is a command line tool for processing HTML. It reads from stdin, prints to stdout, and allows the user to filter parts of the page using css selectors. (https://github.com/EricChiang/pup) 
