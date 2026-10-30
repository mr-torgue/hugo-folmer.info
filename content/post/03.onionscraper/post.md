---
title: OnionScraper
date: '2022-01-15'
---

Some time ago I worked on a small scraper using python scrapy. It is called OnionScraper and it can be downloaded on [github](https://github.com/mr-torgue/OnionScraper).
The goal of OnionScraper is very simple: find onion addresses in data dumps. The reason is that onion addresses are not indexed like regular websites, so it is a bit of trouble to find them.
The first step is to identify sources where onion addresses might be uploaded. I used github gist and pastebin (for now). The second step is to extract the items regularly. This is done by utilizing cron. The cronjob runs every x minutes to get new content uploaded. The third step is to extract onion addresses using a regex, and storing it in a file.

By using regexes it is easily extended to other datatypes. It is still a work in progress. For the next release I will focus on mail notifications, adding more datatypes, adding more sources and making sure that all content is parsed.