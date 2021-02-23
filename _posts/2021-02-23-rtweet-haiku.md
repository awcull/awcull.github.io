---
title: "Haikus In Twitter Using R"
author: "Andrew Cull"
date: "2021-02-23"
output: html_document
---



# Introduction

By using [RTweets](https://github.com/ropensci/rtweet) and [Quanteda](https://quanteda.io/index.html), tweets will be searched by multiple users and analysed if they contain an haiku. An haiku is a poem consisting of three phrases following a pattern of syllables of 5 then 7 then 5. 

Parsing the data will be done with the [dplyr](https://dplyr.tidyverse.org/) package.

# RTweets - Getting and Parsing Twitter Data

On first use of the RTweets package, it will prompt you to log into your account to access API endpoints. The authorization tokens then will be stored in your .Renviron file for later.

In order to get tweet timelines, the function *get_timeline* will be used. Using an array of Twitter users, [Bill Gates](https://twitter.com/BillGates), [Elon Musk](https://twitter.com/elonmusk), and [Wendy's](https://twitter.com/Wendys), the last 5000 tweets will be retrieved. The results contain 90 columns, this will be paired down using *dplyr::filter* to remove retweets, quotes, replies, photos, and only english language.


{% highlight r %}
library(rtweet)
library(dplyr)

tmls <- get_timeline(c("BillGates", "elonmusk", "Wendys"), n=5000)
# Remove retweeted, quotes, and reply tweets
tmls <- tmls %>% filter(is.na(reply_to_user_id) & !is_quote & !is_retweet & is.na(media_type) & lang == "en")

# show sample
knitr::kable(tmls[sample(1:nrow(tmls),5),] %>% select(user_id, created_at, screen_name, text))
{% endhighlight %}



|user_id  |created_at          |screen_name |text                                                                                                                                                                                                           |
|:--------|:-------------------|:-----------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|50393960 |2012-10-09 00:28:18 |BillGates   |“Venture Philanthropy” @SIFPnet helps  social innovators get expertise and advice to make the world a better place. http://t.co/B2VatZA2                                                                       |
|50393960 |2014-10-01 02:32:26 |BillGates   |How can software help teachers adapt to the unique needs of students? Two great examples: @smart_sparrow &amp; @acrobatiq http://t.co/lzCOB4Cb96                                                               |
|50393960 |2012-08-29 18:13:25 |BillGates   |Highlighting a few tools and resources to help teachers during this back to school season. See how to support them: http://t.co/S9UVeOPu                                                                       |
|50393960 |2018-09-15 15:02:24 |BillGates   |New 360 video: they said he was too poor to be a pilot, but Daniel Zuma proved everyone wrong. https://t.co/9l6N83YtXa                                                                                         |
|50393960 |2019-12-01 02:34:24 |BillGates   |The world has made incredible strides toward ending the AIDS epidemic over the last twenty years, and I’m optimistic that more progress is on the way. Explore the data: https://t.co/uQ05HXrrPL #WorldAIDSDay |

There are Tweets with links in the text which can be removed. Link info is held in the data frame under the *urls_t.co* list. There is definitely a better way to do this than the loop below. 


{% highlight r %}
for (k in 1:nrow(tmls)) {
  if (!is.na(tmls$urls_url[[k]])) {
    urls <- tmls$urls_t.co[[k]]
    for (j in 1:length(urls)) {
      tmls$text[k] <- gsub(urls[j], "", tmls$text[k], fixed=T)
    }
  }
}
{% endhighlight %}

A bit more text parsing needs to be done. Trimming white space, removing new lines, and replacing non character values. An additional step needs to be taken, which is not done, is to remove twitpic links, which do not show up in url link columns in the dataframe but are in some tweets.


{% highlight r %}
tmls$text <- trimws(tmls$text) # trim whitespace
tmls$text <- gsub("’", "\\'", tmls$text)
tmls$text <- gsub("[\r\n]", " " , tmls$text) # remove new lines
tmls$text <- gsub(":", "", tmls$text, fixed = T)
tmls$text <- gsub("&amp;", "&", tmls$text, fixed=T)
tmls$text <- gsub("&", "", tmls$text, fixed=T)
tmls$text <- gsub("\\s+", " ", tmls$text)
{% endhighlight %}

# Parse Haiku with Quanteda

Using Quanteda package and the *nsyllable* function to convert each word into the number of syllables and looking for the 5-7-5 syllable count to make a haiku. The simple way, check if number of syllables sum to 17, then check if the 5-7-5 structure is present. There is likely a much better way to do this part.



{% highlight r %}
library(quanteda)

haikus <- NULL

for (k in 1:nrow(tmls)) {
  textSplit <- strsplit(tmls$text[k], "\\s")
  syl <- lapply(textSplit, nsyllable)[[1]]
  if (sum(syl, na.rm=T) == 17) {
    syl[is.na(syl)] <- 0
    cs <- cumsum(syl)
    # Check if this contains 5, 12, 17 (cumsum)
    if (all(c(5,12,17) %in% cs)) {
      # parse out info
      idx_v1 <- 1:which(cs == 5)[1]
      idx_v2 <- (which(cs == 5)[1] + 1):which(cs == 12)[1]
      idx_v3 <- (which(cs == 12)[1] + 1):which(cs == 17)[1]
      haikus <- rbind(haikus, cbind(data.frame(tmls[k,c("user_id","status_id","created_at","screen_name","text")]),  
                                    data.frame(verse1=paste0(textSplit[[1]][idx_v1], collapse=" "), 
                                               verse2=paste0(textSplit[[1]][idx_v2], collapse=" "),
                                               verse3=paste0(textSplit[[1]][idx_v3], collapse=" "))))
    }
  }
}
{% endhighlight %}

# Haikus


{% highlight r %}
knitr::kable(haikus, align="c")
{% endhighlight %}



| user_id  |      status_id      |     created_at      | screen_name |                                         text                                         |          verse1          |              verse2               |             verse3              |
|:--------:|:-------------------:|:-------------------:|:-----------:|:------------------------------------------------------------------------------------:|:------------------------:|:---------------------------------:|:-------------------------------:|
| 50393960 | 734390038482452480  | 2016-05-22 14:26:44 |  BillGates  |    These five books kept me up reading long past when I should have gone to sleep    | These five books kept me |    up reading long past when I    |    should have gone to sleep    |
| 50393960 | 657587998297485312  | 2015-10-23 16:02:50 |  BillGates  |              It's an honor to support the heroes fighting to end polio               |     It's an honor to     |    support the heroes fighting    |          to end polio           |
| 50393960 | 494679070832328704  | 2014-07-31 03:00:55 |  BillGates  |            An introduction to the mind-bending world of quantum computing            |     An introduction      |   to the mind-bending world of    |        quantum computing        |
| 50393960 | 388047506493669377  | 2013-10-09 21:05:09 |  BillGates  |     How #China, @PATHtweets and others lowered the cost of one vaccine by 1000x      | How #China, @PATHtweets  |    and others lowered the cost    |        of one vaccine by        |
| 50393960 | 379877960377204736  | 2013-09-17 08:02:18 |  BillGates  | In 5 years, the number of #Kenyans living with #AIDS has dropped by 200K. Here's why |  In 5 years, the number  |   of #Kenyans living with #AIDS   | has dropped by 200K. Here's why |
| 50393960 | 337343579699675136  | 2013-05-22 23:05:51 |  BillGates  |           Swiss researchers found a clever new way to fight #tuberculosis            | Swiss researchers found  |     a clever new way to fight     |          #tuberculosis          |
| 50393960 | 281152402252500993  | 2012-12-18 21:42:10 |  BillGates  |        I've just posted my favorite reads in 2012. What were your favorites?         |   I've just posted my    | favorite reads in 2012. What were |         your favorites?         |
| 50393960 | 233594623828836352  | 2012-08-09 16:04:32 |  BillGates  |          Want to know what the twittersphere suggested I read this summer?           |  Want to know what the   |      twittersphere suggested      |       I read this summer?       |
| 50393960 | 111442567018725376  | 2011-09-07 14:15:53 |  BillGates  |          Seth's Blog That buzzing in my ear didn't mean I was about to die           | Seth's Blog That buzzing |      in my ear didn't mean I      |        was about to die         |
| 50393960 |  73437168386846720  | 2011-05-25 17:15:59 |  BillGates  |       I like this a lot - Growing Better Rice for a Hungry World (@GOOD stuff)       |    I like this a lot     |    - Growing Better Rice for a    |   Hungry World (@GOOD stuff)    |
| 50393960 |  43066397978398720  | 2011-03-02 21:53:24 |  BillGates  |    The team is getting ready to hit the stage at TED... http//twitpic.com/45ga83     |   The team is getting    |     ready to hit the stage at     | TED... http//twitpic.com/45ga83 |
| 44196397 | 1354891972406865922 | 2021-01-28 20:39:53 |  elonmusk   |           Here come the shorty apologists Give them no respect Get Shorty            |   Here come the shorty   |      apologists Give them no      |       respect Get Shorty        |
| 44196397 | 1279875995516801024 | 2020-07-05 20:33:10 |  elonmusk   |                 Read The Story of Civilization by Will Ariel Durant                  |    Read The Story of     |       Civilization by Will        |          Ariel Durant           |
| 59553554 | 1362440584418398211 | 2021-02-18 16:35:22 |   Wendys    |         The next big thing in chicken tech? Tune in to find out. #innovation         |  The next big thing in   |   chicken tech? Tune in to find   |        out. #innovation         |
