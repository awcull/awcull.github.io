---
title: "The 40% of Bender"
date: "2021-03-22"
output: html_document
layout: post
categories: [R, futurama, television, bender]
comments: true
---



# Bender's Composition

Every once in awhile bender proclaims he is '40 percent' of something. What has he said he is made out of:

<style>
tr:nth-child(even) {
  background-color: #e8e8e8;
}
table {
  border: 1px solid #ddd;
}
</style>

<table class="table table-striped" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> Benders Composition </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> im 40% zinc </td>
  </tr>
  <tr>
   <td style="text-align:left;"> my bodys 40% titanium im finally richer than those snooty atm machines </td>
  </tr>
  <tr>
   <td style="text-align:left;"> im 40% dolomite oh its hot its very hot </td>
  </tr>
  <tr>
   <td style="text-align:left;"> oh no im 40% lucky the scrap metal im made from included a truckload of horseshoes from the luckiest racehorses in mexico who had just been sent to a glue factory </td>
  </tr>
  <tr>
   <td style="text-align:left;"> im 40% scrap metal </td>
  </tr>
  <tr>
   <td style="text-align:left;"> im 40% wire </td>
  </tr>
  <tr>
   <td style="text-align:left;"> im 40% empty also what the hell is a free will slot </td>
  </tr>
</tbody>
</table>



# Code

This was based off of previous [Futurama: Benders Top 10 Words](https://www.awcull.com/2016/05/28/Benders-Most-Freq-Words.html).

{% highlight r %}
library(httr)
library(XML)
library(kableExtra)

base_site <- "http://theinfosphere.org"

## Get Links
# XPATH
linkXPath <- "//*[contains(concat( ' ', @class, ' ' ), concat( ' ', 'oLeft', ' ' ))]//a/@href"
# Path to transcript listings
ts_path <- "Episode_Transcript_Listing"
# Get it
pageL <- GET(base_site, path=ts_path)
# Convert to HTML
h <- htmlParse(pageL)
# Provided by SelectorGadget... /@href was added as only want the link part
hLinks <- getNodeSet(h, linkXPath)
# Convert to character
hLinks <- as.character(hLinks)

# This function will be used to clean up each episode
getBenDialog <- function(diag) {
  diag <- diag[grepl("Bender: ", diag)]
  # From end of "Bender: " to character before of \n
  found <- regexpr("(?<=Bender: ).*?(?=\\\n)", diag, perl=T) 
  diag <- ifelse(found == -1, NA, regmatches(diag, found))
  # Remove anything between square brackets [], regex "\\[[^\\]]*\\]"
  diag <- gsub("\\[[^\\]]*\\]", "", diag, perl=T)
  diag <- gsub("[^[:alnum:][:space:]%]", "", diag) # Remove punctuation
  diag <- gsub("\\s+", " ", diag) # Remove white space
  diag <- gsub("^\\s+|\\s+$", "", diag) # Remove leading/trailing whitespace
  diag <- tolower(diag)
  return(diag)
}

# Output
benDialog <- NULL
# Loop over each episode and get data
for (k in 1:length(hLinks)) {
  # Get episode
  pageT <- GET(paste0(base_site, hLinks[k]))
  h <- htmlParse(pageT, asText=TRUE)
  # XPaths
  diagXPath <- "//p"
  diag <- xpathSApply(h, diagXPath, xmlValue)
  # Process Episode
  benDiag <- getBenDialog(diag)
  # Remove words, add it to vector
  benDialog <- c(benDialog, benDiag)
  # Be nice
  Sys.sleep(1)
}
benDialog <- benDialog[!is.na(benDialog)]
benDialog <- unique(benDialog)

knitr::kable(data.frame(benderComposition=benDialog[grepl("40%",benDialog)])) %>% kable_styling(bootstrap_options = c("striped"))
{% endhighlight %}