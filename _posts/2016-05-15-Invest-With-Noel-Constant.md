---
title: "Investing with Noel Constant"
date: "2016-05-15"
layout: post
categories: [R, investment, Kurt Vonnegut, Sirens of Titan, Books]
comments: true
output: html_document
---

Nothing here constitutes financial advise!!! If it wasn't obvious.

# Introduction

Noel Constant is a fictional character in Kurt Vonnegut's The Sirens of Titan.  In the book, Noel amassed his fortune through a rather peculiar investment strategy, the Gideon Bible.  From the book (The Sirens of Titan, page 98), he started with $8200 and became one of the richest people on Earth.  He did this by taking the Bible, took each sentence, capitalized the letters, put periods between each letter, and divid each sentence into pairs of letters.  Using this, he would invest his money into the first company matching the pair of letters and sell it immediately once it doubled in price.  In this post, we will look at what companies Noel would have invested in, if he would start at the time of writing (2015-05-15).

# Data

The first sentence of the Gideon Bible, according to The Sirens of Titan (page 99) is: "In the beginning God created the heaven and the earth."  Now, Noel Constant using this system would make the sentence into: "I.N, T.H, E.B, ....".  

## Bible Version

I have unfortunately not been able to obtain the Gideon version of the bible, but was able to locate the [King James Version from Project Gutenberg](http://www.gutenberg.org/cache/epub/10/pg10.txt) available in plain text.

To do this, the tm package will be employed so that filtering of the data will be easily done.  This will involve removing numbers and punctuation.


{% highlight r %}
fP <- "/Volumes/Lexar/web/titan/KJV.txt"
kjv <- readLines(fP)
head(kjv)
{% endhighlight %}



{% highlight text %}
## [1] "The Project Gutenberg EBook of The King James Bible"                 
## [2] ""                                                                    
## [3] "This eBook is for the use of anyone anywhere at no cost and with"    
## [4] "almost no restrictions whatsoever.  You may copy it, give it away or"
## [5] "re-use it under the terms of the Project Gutenberg License included" 
## [6] "with this eBook or online at www.gutenberg.org"
{% endhighlight %}

Unfortunately, there is quite a bit of header information that needs to be removed for parsing.  If we take a look at the text file, we can see where the book starts and ends.  For this file we are looking for the following two sentences: 'START OF THIS PROJECT GUTENBERG EBOOK' and 'End of the Project Gutenberg EBook'.


{% highlight r %}
bStart <- grep("START OF THIS PROJECT GUTENBERG EBOOK", kjv)+1 # +1 as want the next line
bEnd <- grep("End of the Project Gutenberg EBook", kjv)-1 # -1 as want the last line of the book
kjv <- kjv[bStart:bEnd]
head(kjv)
{% endhighlight %}



{% highlight text %}
## [1] "" "" "" "" "" ""
{% endhighlight %}

Seems now that that new line spaces are being taken up the space.  Removing these to try and get closer to the actual text.


{% highlight r %}
kjv <- kjv[kjv != ""]
head(kjv)
{% endhighlight %}



{% highlight text %}
## [1] "The Old Testament of the King James Version of the Bible"              
## [2] "The First Book of Moses:  Called Genesis"                              
## [3] "1:1 In the beginning God created the heaven and the earth."            
## [4] "1:2 And the earth was without form, and void; and darkness was upon"   
## [5] "the face of the deep. And the Spirit of God moved upon the face of the"
## [6] "waters."
{% endhighlight %}

Just about there.  According to The Sirens of Titan, the title of the parts of the bible was not considered, eg "The First Book of Moses: Called Genesis".  All titles of the parts of the bible will need to be removed, fortunately they can be parsed easily due to the structure of verses and that we removed new lines earlier.


{% highlight r %}
vStart <- grep("^1:1 ", kjv) # Start of each verse is 1:1 for each book.
# Each title is one spot back, taking a look at these
head(kjv[vStart-1])
{% endhighlight %}



{% highlight text %}
## [1] "The First Book of Moses:  Called Genesis"    
## [2] "The Second Book of Moses:  Called Exodus"    
## [3] "The Third Book of Moses:  Called Leviticus"  
## [4] "The Fourth Book of Moses:  Called Numbers"   
## [5] "The Fifth Book of Moses:  Called Deuteronomy"
## [6] "The Book of Joshua"
{% endhighlight %}



{% highlight r %}
# Now to remove these from the vector
kjv <- kjv[-c(vStart-1)]
head(kjv)
{% endhighlight %}



{% highlight text %}
## [1] "The Old Testament of the King James Version of the Bible"              
## [2] "1:1 In the beginning God created the heaven and the earth."            
## [3] "1:2 And the earth was without form, and void; and darkness was upon"   
## [4] "the face of the deep. And the Spirit of God moved upon the face of the"
## [5] "waters."                                                               
## [6] "1:3 And God said, Let there be light: and there was light."
{% endhighlight %}



{% highlight r %}
# The title of the Bible is still there as the first line, so remove that
kjv <- kjv[-1]
head(kjv)
{% endhighlight %}



{% highlight text %}
## [1] "1:1 In the beginning God created the heaven and the earth."            
## [2] "1:2 And the earth was without form, and void; and darkness was upon"   
## [3] "the face of the deep. And the Spirit of God moved upon the face of the"
## [4] "waters."                                                               
## [5] "1:3 And God said, Let there be light: and there was light."            
## [6] "1:4 And God saw the light, that it was good: and God divided the light"
{% endhighlight %}

Now using the tm package, to start removing numbers, punctuation, and extra white space.


{% highlight r %}
library(tm)
{% endhighlight %}


{% highlight r %}
kjv <- paste(kjv, collapse="") # Collapse every line into one big one
kjv.tm <- VectorSource(kjv)
rm(kjv)
kjv.cor <- Corpus(kjv.tm)
rm(kjv.tm)

# Remove punctuation
kjv.cor <- tm_map(kjv.cor, removePunctuation)
# Remove numbers
kjv.cor <- tm_map(kjv.cor, removeNumbers)
# Remove any white space that may of been generated from this
kjv.cor <- tm_map(kjv.cor, stripWhitespace)
# Strip spacings
kjv.cor <- tm_map(kjv.cor, removeWords, " ")
kjv.cor <- tm_map(kjv.cor, function (x) gsub("^\\s+|\\s+$", "", x)) # remove leading and trailing white space which the above doesn't seem to do
# Capitalize letters
kjv.cor <- tm_map(kjv.cor, toupper)
{% endhighlight %}

The last bit is now to separate the string into groups of two characters:


{% highlight r %}
sym <- substring(kjv.cor[[1]], seq(1, nchar(kjv.cor[[1]]),2), seq(2, nchar(kjv.cor[[1]]),2))
head(sym)
{% endhighlight %}



{% highlight text %}
## [1] "IN" "TH" "EB" "EG" "IN" "NI"
{% endhighlight %}

Now, the next step will be to take these 'stock symbols' and get their trading history.

## Obtaining Stock Quotes

Using quantmod, the individual stocks can be queried and attempted to see how fruitful his investments would of been.  Since, not sure where he is investing and when, the NASDAQ, NYSE, and AMEX stock markets will be used.


{% highlight r %}
library(quantmod)
{% endhighlight %}

{% highlight r %}
## Get list of available companies
ex.sym <- stockSymbols()
{% endhighlight %}



{% highlight text %}
## Fetching AMEX symbols...
## Fetching NASDAQ symbols...
## Fetching NYSE symbols...
{% endhighlight %}

Now, we have both symbols from the Bible and stock symbols, we can reduce the symbols generated from the Bible.


{% highlight r %}
## Get only stock symbols that there are companies for
sym.r <- sym[sym %in% ex.sym$Symbol]
head(sym.r)
{% endhighlight %}



{% highlight text %}
## [1] "NI" "NG" "RE" "AT" "ED" "EA"
{% endhighlight %}



Now if we would want to grab historical data for a stock, _getSymbols_ function can be used such as:

<p style="text-align: center;"> getSymbols("AAPL", from=as.Date("1900-01-01"), to=Sys.Date()) </p>

Since the range of dates and truly investing from the future is much harder, this will not be done.  Issues that come up are, choosing a starting date, removing stocks whos IPO maybe to new, and stocks that don't double in price.  For the purpose of this, we will just look at in a hypothetical perfect world what Noel would have invested in.

## Invested Companies

Looking at the invested companies, a large data frame will be created with each company invested in symbol, IPO date, company name, last sale price, market cap, sector, industry, and the exchange it is listed under.  


{% highlight r %}
ci.df <- data.frame(symbol=sym.r, ex.sym[sym.r %in% ex.sym$Symbol, 2:ncol(ex.sym)])
{% endhighlight %}

To take a look at what Noel would be investing in:

Number of Investments, if he worked all the way through the Bible:

{% highlight r %}
nrow(ci.df) # Number of total investments if went all the way through the bible
{% endhighlight %}



{% highlight text %}
## [1] 728474
{% endhighlight %}

Total number of unique companies invested in:

{% highlight r %}
length(unique(ci.df$symbol)) # number of unique companies
{% endhighlight %}



{% highlight text %}
## [1] 213
{% endhighlight %}

Look at first few investments:

{% highlight r %}
kable(head(ci.df), align='c')
{% endhighlight %}



| symbol |                              Name                              | LastSale | MarketCap | IPOyear |      Sector      |            Industry             | Exchange |
|:------:|:--------------------------------------------------------------:|:--------:|:---------:|:-------:|:----------------:|:-------------------------------:|:--------:|
|   NI   |                           Alcoa Inc.                           |  88.010  |    NA     |   NA    |  Capital Goods   |       Metal Fabrications        |   AMEX   |
|   NG   |                Altisource Asset Management Corp                |  12.510  |  $22.44M  |   NA    |     Finance      |           Real Estate           |   AMEX   |
|   RE   |                     Almaden Minerals, Ltd.                     |  1.120   |  $87.58M  |  2015   | Basic Industries |         Precious Metals         |   AMEX   |
|   AT   | Aberdeen Emerging Markets Smaller Company Opportunities Fund I |  11.830  | $115.16M  |   NA    |        NA        |               NA                |   AMEX   |
|   ED   |                    Acme United Corporation.                    |  17.290  |  $57.62M  |  1988   |  Capital Goods   | Industrial Machinery/Components |   AMEX   |
|   EA   |                       AeroCentury Corp.                        |  9.215   |  $14.44M  |   NA    |    Technology    | Diversified Commercial Services |   AMEX   |

The complete data frame can be [downloaded here.](http://awcull.com/assets/2016-05-15-Invest-With-Noel-Constant/invest.csv)

## Other

For repeatability, my session info:


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
## [1] quantmod_0.4-5 TTR_0.23-1     xts_0.9-7      zoo_1.7-12    
## [5] tm_0.6-2       NLP_0.1-9      knitr_1.11    
## 
## loaded via a namespace (and not attached):
##  [1] lattice_0.20-33 slam_0.1-32     grid_3.2.1      formatR_1.2.1  
##  [5] magrittr_1.5    evaluate_0.8    highr_0.5.1     stringi_0.5-5  
##  [9] tools_3.2.1     stringr_1.0.0   parallel_3.2.1
{% endhighlight %}
