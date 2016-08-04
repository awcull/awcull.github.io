---
title: 'Futurama: Benders Top 10 Words'
date: "2016-05-28"
layout: post
categories: [R, futurama, tv]
comments: true
output: html_document
---


{% highlight r %}
library(knitr)
{% endhighlight %}

## Introduction

During season 3 episode 2 titled War Is The H-Word, a database of Bender's top 10 most frequently used words are shown.  Some words like 'pimpmobile' I would think are not uttered a lot by Bender, rather was used for the setup for the episode.  This got me thinking, what are the most frequently used words by Bender on air.

From the episode the top 10 words are:


| Number |    Word    |
|:------:|:----------:|
|   10   |   Chump    |
|   9    | Chumpette  |
|   8    |   Yours    |
|   7    |     Up     |
|   6    | Pimpmobile |
|   5    |    Bite    |
|   4    |     My     |
|   3    |   Shiny    |
|   2    |  Daffodil  |
|   1    |    Ass     |

## Episode Transcripts Data

The data will be pulled from the [Infosphere website](https://theinfosphere.org/Episode_Transcript_Listing).  Unforunately, this does not have all seasons but will make due with what is available.  To get the HTML data, the package [httr](https://cran.r-project.org/web/packages/httr/index.html) will be used.  [SelectorGadget](http://selectorgadget.com/) can still be used to gather the proper XPath to be used.

First we are going to load the libraries, set up base site address, and create the certification file.


{% highlight r %}
library(httr)
library(XML)

base_site <- "https://theinfosphere.org"
# Define cert file
cafile <- system.file("CurlSSL", "cacert.pem", package="RCurl")
{% endhighlight %}


# Episode Transcript Links

Each episode is listed by season in a table with a link to the respective episode transcript.  Using SelectorGadget, the XPath for the title is: //*[contains(concat( " ", @class, " " ), concat( " ", "oLeft", " " ))]//a.  Using this will give us the href and title, we are only concerned with the href part.  By adding /@href to the XPath, only the links will be returned.  This will be converted to a character vector and the


{% highlight r %}
# XPATH
linkXPath <- "//*[contains(concat( ' ', @class, ' ' ), concat( ' ', 'oLeft', ' ' ))]//a/@href"

# Path to transcript listings
ts_path <- "Episode_Transcript_Listing"

# Get it
pageL <- GET(base_site, path=ts_path, config(cainfo=cafile))

# Convert to HTML
h <- htmlParse(pageL)

# Provided by SelectorGadget... /@href was added as only want the link part
hLinks <- getNodeSet(h, linkXPath)

# Convert to character
hLinks <- as.character(hLinks)
{% endhighlight %}

Now there is the issue of the direct to DVD movies.  These movies were shown in 4 parts each on Comedy Central, so if all transcripts are included we will be double counting these.  The four movies: Bender's Big Score, The Beast with a Billion Backs, Bender's Game, and Into the Wild Green Yonder will be removed, leaving the four parts of each.

This is not the best way to do this but it will work for our purposes.  A better way would be to have scraped table information first, removed the Direct-To-DVD film table and then got the links from the remaining season tables.


{% highlight r %}
# We know the films already from the web site
filmLinks <- c("/Transcript:Into_the_Wild_Green_Yonder", "/Transcript:Bender%27s_Game", 
               "/Transcript:The_Beast_with_a_Billion_Backs", "/Transcript:Bender%27s_Big_Score")
hLinks <- hLinks[!(hLinks %in% filmLinks)]
{% endhighlight %}

Next step will be to parse each episode for Bender's dialog.

# Episode Transcript

Getting the transcripts for each episode is a bit more involved.  Although a [style](https://theinfosphere.org/Infosphere:Transcript) is introduced, getting the actual text for Bender is a little bit more work.  From using SelectorGadget on the site, all dialog can be obtained with the "//p" XPath.  


{% highlight r %}
diagXPath <- "//p"

pageT <- GET(paste0(base_site, hLinks[1]), config(cainfo=cafile))
h <- htmlParse(pageT, asText=TRUE)
diag <- xpathSApply(h, diagXPath, xmlValue)
head(diag)
{% endhighlight %}



{% highlight text %}
## [1] "(00:07) <U+2A02>Fry: [voice-over] Space. It seems to go on and on forever. But then you get to the end and the gorilla starts throwing barrels at you.\n"                      
## [2] "(00:20) <U+2A02>Fry: And that's how you play the game!\n"                                                                                                                      
## [3] "(00:22) <U+2A02>Boy: You stink, loser!\n"                                                                                                                                      
## [4] "(00:24) <U+2A02>Panucci: Hey, Fry. Pizza goin' out! C'mon!!\n"                                                                                                                 
## [5] "(00:38) <U+2A02>Fry: Michelle, baby! Where you going?\n"                                                                                                                       
## [6] "(00:41) <U+2A02>Michelle: It's not working out, Fry. [Next to her is a guy with his arm around her. The cab pulls away.] [shouting; from cab] I put your stuff out on the sidewalk!\n"
{% endhighlight %}

We will need to clean up the text.  The way I will process this, is to:
- Read in the transcripts
- Filter only for Bender ("Bender: " as per Rules/Styles)
- Create a substring from after "Bender:" to the end of the string
- Remove anything between square brackets as they are a description
- Remove punctuation 
- Remove extra white space
- Lower case all letters.
- Remove common words like: "to","too", "i", "its","ill", "im", "a", "an", "and", "at", "are","on", "be", "the", "that", "for". The function stopWords in _tm_ package can be used but would remove 


{% highlight r %}
library(tm)

# This function will be used to clean up each episode
getBenDialog <- function(diag) {
  diag <- diag[grepl("Bender: ", diag)]
  # From end of "Bender: " to character before of \n
  found <- regexpr("(?<=Bender: ).*?(?=\\\n)", diag, perl=T) 
  diag <- ifelse(found == -1, NA, regmatches(diag, found))
  # Remove anything between square brackets [], regex "\\[[^\\]]*\\]"
  diag <- gsub("\\[[^\\]]*\\]", "", diag, perl=T)
  diag <- gsub("[[:punct:]]", "", diag) # Remove punctuation
  diag <- gsub("\\s+", " ", diag) # Remove white space
  diag <- gsub("^\\s+|\\s+$", "", diag) # Remove leading/trailing whitespace
  diag <- tolower(diag)
  return(diag)
}

# remWords <- c("to","too", "i", "its","ill", "im", "a", "an", "and", "at", "are","on", "be", "than", "the", "that", "for", "do", "was", "what", "were", "well", "it", "can", "this", "you", "here", "dont", "didnt", "have", "my", "by", "so", "in", "of", "thats", "as", "we", "as", "cant", "go", "ok", "not")
remWords <- stopwords("en")
remWords <- gsub("[[:punct:]]", "", remWords)
remWords <- remWords[!(remWords %in% tolower(c("Chump","Chumpette","Yours", "Up", "Pimpmobile", "Bite", "My", "Shiny", "Daffodil", "Ass")))]
benDiag <- getBenDialog(diag)
head(benDiag)
{% endhighlight %}



{% highlight text %}
## [1] "bite my shiny metal ass"                                                                  
## [2] "shinier than yours meatbag"                                                               
## [3] "listen buddy im in a hurry here lets try for a twofer"                                    
## [4] "great choice bring it on baby"                                                            
## [5] "cmon cmon kill me already by the way my names bender"                                     
## [6] "lousy stinking ripoff well i didnt have anything else planned for today lets go get drunk"
{% endhighlight %}



{% highlight r %}
## Now want to remove words that are 'common'
benDiag <- unlist(sapply(benDiag, function(x, s) { t <- tolower(unlist(strsplit(x, " "))); t[!(t %in% s)]}, s = remWords), use.names=F)
head(benDiag)
{% endhighlight %}



{% highlight text %}
## [1] "bite"    "my"      "shiny"   "metal"   "ass"     "shinier"
{% endhighlight %}

Putting this all together for each episode:


{% highlight r %}
library(httr)
library(XML)
library(tm)

base_site <- "https://theinfosphere.org"
# Define cert file
cafile <- system.file("CurlSSL", "cacert.pem", package="RCurl")

## Get Links
# XPATH
linkXPath <- "//*[contains(concat( ' ', @class, ' ' ), concat( ' ', 'oLeft', ' ' ))]//a/@href"
# Path to transcript listings
ts_path <- "Episode_Transcript_Listing"
# Get it
pageL <- GET(base_site, path=ts_path, config(cainfo=cafile))
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
  diag <- gsub("[[:punct:]]", "", diag) # Remove punctuation
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
  pageT <- GET(paste0(base_site, hLinks[k]), config(cainfo=cafile))
  h <- htmlParse(pageT, asText=TRUE)
  # XPaths
  diagXPath <- "//p"
  diag <- xpathSApply(h, diagXPath, xmlValue)
  # Process Episode
  benDiag <- getBenDialog(diag)
  # Remove words, add it to vector
  benDialog <- c(benDialog,
               unlist(sapply(benDiag, function(x, s) { t <- tolower(unlist(strsplit(x, " "))); t[!(t %in% s)]}, s = remWords), use.names=F))
  # Be nice
  Sys.sleep(1)
}
benDialog <- benDialog[!is.na(benDialog)]
{% endhighlight %}



## Bender's Top 10 Most Frequented Words

This can be cleaned up even further but playing with the removed words if needed.  As well, this relies on the data on Infosphere being correct and complete.


| Number |  Word  |
|:------:|:------:|
|   10   |  now   |
|   9    |  yeah  |
|   8    |   up   |
|   7    | bender |
|   6    |  like  |
|   5    |  one   |
|   4    |  hey   |
|   3    |  just  |
|   2    |   oh   |
|   1    |   my   |

Bender's most frequent used word "ass" ranks: 45 

The scraped processed data can be found [here](http://awcull.com/assets/2016-05-28-Benders-Most-Freq-Words/benWords.txt).


{% highlight r %}
sessionInfo()
{% endhighlight %}



{% highlight text %}
## R version 3.2.0 (2015-04-16)
## Platform: x86_64-w64-mingw32/x64 (64-bit)
## Running under: Windows 8 x64 (build 9200)
## 
## locale:
## [1] LC_COLLATE=English_United States.1252 
## [2] LC_CTYPE=English_United States.1252   
## [3] LC_MONETARY=English_United States.1252
## [4] LC_NUMERIC=C                          
## [5] LC_TIME=English_United States.1252    
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] tm_0.6-2     NLP_0.1-9    XML_3.98-1.4 httr_1.1.0   knitr_1.12.3
## 
## loaded via a namespace (and not attached):
##  [1] R6_2.1.2       magrittr_1.5   formatR_1.3    parallel_3.2.0
##  [5] tools_3.2.0    curl_0.9.6     slam_0.1-32    stringi_1.0-1 
##  [9] highr_0.5.1    stringr_1.0.0  evaluate_0.8.3
{% endhighlight %}
