---
title: "Scrape Overwatch Data with Rvest"
output: html_document
date: "2016-07-19"
layout: post
categories: [R, Overwatch, rvest]
comments: true
---

## Introduction

[Blizzard's Overwatch]() is a team based first person shooter with over 20 unique heroes available on pc, XBox, and Playstation.  The goal is to use a team of 6 to move a payload to a location, capture an objective, or a hybrid of both payload and capture.  Blizzard stores information about your performance such as damage done, healing done, objective time, etc. on their website.  Unfortunately, at the time of writing this, there is no official API available for Overwatch, so we will need to scrape the website to get this information.

## Data

For this example, we will be using [this profile](https://playoverwatch.com/en-gb/career/pc/us/polar-1188).  The data is split into two different play categories, *quick play* and *competitive play*.  Each play mode has information for each played heroes in that mode.  The hero data information for the play modes will be what we are after.

# URL

The URL (example: https://playoverwatch.com/en-gb/career/pc/us/polar-1188) can be broken down into 3 important parts, *pc*, *us*, and *polar-1188*.  This is:

1. *platform* - computer (pc), XBox (xbl), or Playstation (psn).
2. *region* - North America (us), Europe (eu),  Korea (kr), or China (cn)
3. *battle tag* - Unique identifier of the player.

So, if the player foobar-1234 was playing on the Korean servers on a computer the last three arguments become /pc/kr/foobar-1234.

# Website data

There is a lot of information presented initially on the page.  The featured stats and a drop down menu with top hero, win percent, etc. are all based off of the *Career Stats* tables.  It is these tables in career stats that we will be scraping.

The website breaks information down into either *quick or competitive play* and to obtain the information for each the page source shows it is a node with either "#quick-play" or "#competitive-play"

The last part that needs to be addressed is selecting each table from the career stats node.  Since each hero will have mostly the same tables such as combat, hero specific, etc. we will need to get the correct hero.  This selector will look like this ("select > option"):

{% highlight html %}
<select data-group-id="stats" class="js-career-select dropdown_original">
<option value="0x02E00000FFFFFFFF">ALL HEROES</option>
<option value="0x02E0000000000002">Reaper</option>
<option value="0x02E0000000000003">Tracer</option>
<option value="0x02E0000000000004">Mercy</option>
<option value="0x02E0000000000005">Hanzo</option>
<option value="0x02E0000000000006">Torbjörn</option>
<option value="0x02E0000000000007">Reinhardt</option>
<option value="0x02E0000000000008">Pharah</option>
<option value="0x02E0000000000009">Winston</option>
<option value="0x02E000000000000A">Widowmaker</option>
<option value="0x02E0000000000016">Symmetra</option>
<option value="0x02E0000000000020">Zenyatta</option>
<option value="0x02E0000000000029">Genji</option>
<option value="0x02E0000000000040">Roadhog</option>
<option value="0x02E0000000000042">McCree</option>
<option value="0x02E0000000000065">Junkrat</option>
<option value="0x02E0000000000068">Zarya</option>
<option value="0x02E000000000006E">Soldier: 76</option>
<option value="0x02E0000000000079">Lúcio</option>
<option value="0x02E00000000000DD">Mei</option></select>"
{% endhighlight %}

The value will have to be matched to the table to know what hero we are dealing with.

# Scrape the Data


{% highlight r %}
library(rvest)

# storage info
quickTables <- NULL
compTables <- NULL
btag <- "polar-1188" # storage name

url <- "https://playoverwatch.com/en-gb/career/pc/us/polar-1188"

# Obtain URL, if url found return the data, else "err message"
webHTML <- tryCatch({read_html(url)}, error=function(err) "Error")

# Game category - obtained from inspection (2016-07-19)
gameCat <- c("#quick-play", "#competitive-play")

## Loop over game cats
for (whichGC in 1:length(gameCat)) {
  # Get playmode
  innerNodes <- webHTML %>% html_nodes(gameCat[1])
  
  # Get selectors, could be different between quick and comp
  idNodes <- innerNodes %>% html_nodes("select > option")
  idAttr <- idNodes %>% html_attr("value")
  heroName <- idNodes %>% html_text()
  
  # table sections
  tabSection <- innerNodes %>% html_nodes("div[data-group-id='stats']")
  # Loop over all tables
  for (whichTable in 1:length(tabSection)) {
    # Get table id
    tabID <- tabSection[whichTable] %>% html_attr("data-category-id")
    tabSel <- paste0("div[data-category-id=", "'", tabID, "'", "]") # select the given table
    # Get table information
    dataTables <- innerNodes %>% html_nodes(tabSel) %>% html_nodes("table.data-table")
    allTables <- dataTables %>% html_table
    ## Now, up to you how you want to store the data... Will just store as vector of data frames
    whichHero <- heroName[grep(tabID, idAttr)]
    # just going to set the names of each data frame in the list
    names(allTables) <- rep(paste0(btag, "-", whichHero, "-",as.character(unlist(lapply(allTables, function(x) { names(x)[1]})))), 1, length(allTables))
    # store in proper one
    if (gameCat[whichGC] == "#quick-play") {
      quickTables <- c(quickTables, allTables)
    } else {
      compTables <- c(compTables, allTables)
    }
  }
}
{% endhighlight %}

Taking a quick look at some tables, using knitr::kable for table formatting:


{% highlight r %}
library(knitr)

kable(compTables[[1]], align="c", caption=names(compTables[1]))
{% endhighlight %}

**polar-1188-ALL HEROES-Combat**

|       Combat        |  Combat   |
|:-------------------:|:---------:|
|  Melee Final Blows  |    99     |
|     Solo Kills      |   2,290   |
|   Objective Kills   |   3,579   |
|     Final Blows     |   5,622   |
|     Damage Done     | 3,803,115 |
|    Eliminations     |  10,242   |
| Environmental Kills |    17     |
|     Multikills      |    96     |

## Conclusion

This shows how to scrape data off of Blizzard's Overwatch site.  More work needs to be done to format values correctly and set up a proper storage of the data if doing it for more than a few IDs.


{% highlight r %}
sessionInfo()
{% endhighlight %}



{% highlight text %}
## R version 3.2.1 (2015-06-18)
## Platform: x86_64-apple-darwin13.4.0 (64-bit)
## Running under: OS X 10.11.4 (unknown)
## 
## locale:
## [1] en_CA.UTF-8/en_CA.UTF-8/en_CA.UTF-8/C/en_CA.UTF-8/en_CA.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] knitr_1.11  plyr_1.8.4  rvest_0.3.2 xml2_1.0.0 
## 
## loaded via a namespace (and not attached):
##  [1] Rcpp_0.12.1     XML_3.98-1.3    digest_0.6.8    R6_2.1.1       
##  [5] magrittr_1.5    evaluate_0.8    highr_0.5.1     httr_1.1.0     
##  [9] stringi_0.5-5   curl_0.9.6      rmarkdown_0.9.2 tools_3.2.1    
## [13] stringr_1.0.0   selectr_0.2-3   yaml_2.1.13     htmltools_0.2.6
{% endhighlight %}
