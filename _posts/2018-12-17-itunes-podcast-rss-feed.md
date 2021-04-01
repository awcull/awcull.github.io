---
title: "Podcast RSS Feeds From iTunes"
author: "Andrew Cull"
date: "2018-12-17"
categories: [R, podcast, iTunes, rss]
layout: post
comments: true
output: html_document
---

## Introduction

Podcasts are typically distributed through RSS feeds, which is an extension onto XML.  These will include episode titles, description, category, and more.  Podcasts will submit their RSS feeds to different platforms like [iTunes](https://itunes.apple.com/ca/genre/podcasts/id26), [Google Podcasts](https://play.google.com/store/apps/details?id=com.google.android.apps.podcasts), and many independent sites.  

The goal of this will be use the [iTunes API](https://affiliate.itunes.apple.com/resources/documentation/itunes-store-web-service-search-api/) and [iTunes RSS Generator](https://rss.itunes.apple.com/en-us) to get a list podcasts, then get the RSS feed for each.

To do this the R package [jsonlite](https://cran.r-project.org/web/packages/jsonlite/index.html) will be used to parse the JSON requests.

## List of Podcasts

To get the list of podcasts, the [iTunes RSS Generator](https://rss.itunes.apple.com/en-us) is used.  Setting the parameters on the site,

- Country: United States
- Media Type: Podcasts
- Feed Type: Top Podcasts
- Genre: All
- Results limit: 100
- Format: JSON
- Allow explicit: Yes

produces the following:


{% highlight html %}
https://rss.itunes.apple.com/api/v1/us/podcasts/top-podcasts/all/100/explicit.json
{% endhighlight %}


Using this, we can start the process:


{% highlight r %}
library(jsonlite) # load package

podcasts <- fromJSON("https://rss.itunes.apple.com/api/v1/us/podcasts/top-podcasts/all/100/explicit.json")
{% endhighlight %}

The results are a list containing the header information related to the query and a data frame of the results we want.


{% highlight r %}
podcastList <- podcasts$feed$results
knitr::kable(head(podcastList), align = "c")
{% endhighlight %}

<div class="table-wrapper" markdown="block">


|        artistName         |     id     | releaseDate |           name           |  kind   |               copyright                |  artistId  | contentAdvisoryRating |                              artistUrl                               |                                                      artworkUrl100                                                      |                                                                                                                                                                                   genres                                                                                                                                                                                    |                                      url                                      |
|:-------------------------:|:----------:|:-----------:|:------------------------:|:-------:|:--------------------------------------:|:----------:|:---------------------:|:--------------------------------------------------------------------:|:-----------------------------------------------------------------------------------------------------------------------:|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|:-----------------------------------------------------------------------------:|
|          Wondery          | 1421573955 | 2018-12-12  |        Dr. Death         | podcast |          © 2018 Wondery, Inc.          | 1134742667 |       Explicit        |      https://itunes.apple.com/us/artist/wondery/1134742667?mt=2      | https://is2-ssl.mzstatic.com/image/thumb/Music128/v4/ec/99/64/ec996492-dec4-5e86-69bc-c9984f512305/source/200x200bb.png |                                                                  list(genreId = c("1315", "26", "1307"), name = c("Science & Medicine", "Podcasts", "Health"), url = c("https://itunes.apple.com/us/genre/id1315", "https://itunes.apple.com/us/genre/id26", "https://itunes.apple.com/us/genre/id1307"))                                                                   |        https://itunes.apple.com/us/podcast/dr-death/id1421573955?mt=2         |
| L.A. Times &#124; Wondery | 1272970334 | 2018-11-21  |        Dirty John        | podcast |      © 2017 All Rights Reserved.       | 1134742667 |          NA           | https://itunes.apple.com/us/artist/l-a-times-wondery/1134742667?mt=2 | https://is2-ssl.mzstatic.com/image/thumb/Music125/v4/ce/3d/b4/ce3db4c8-86ba-29ca-8cb0-4cd68388efeb/source/200x200bb.png |                                                             list(genreId = c("1302", "26", "1324"), name = c("Personal Journals", "Podcasts", "Society & Culture"), url = c("https://itunes.apple.com/us/genre/id1302", "https://itunes.apple.com/us/genre/id26", "https://itunes.apple.com/us/genre/id1324"))                                                              |       https://itunes.apple.com/us/podcast/dirty-john/id1272970334?mt=2        |
|         Joe Rogan         | 360084272  | 2018-12-12  | The Joe Rogan Experience | podcast | Copyright © Talking Monkey Productions | 974891224  |       Explicit        |     https://itunes.apple.com/us/artist/joe-rogan/974891224?mt=2      | https://is1-ssl.mzstatic.com/image/thumb/Music127/v4/d0/e6/5f/d0e65f81-c2cf-7f59-38e4-6abcfab7e38a/source/200x200bb.png | list(genreId = c("1303", "26", "1318", "1450", "1324"), name = c("Comedy", "Podcasts", "Technology", "Podcasting", "Society & Culture"), url = c("https://itunes.apple.com/us/genre/id1303", "https://itunes.apple.com/us/genre/id26", "https://itunes.apple.com/us/genre/id1318", "https://itunes.apple.com/us/genre/id1450", "https://itunes.apple.com/us/genre/id1324")) | https://itunes.apple.com/us/podcast/the-joe-rogan-experience/id360084272?mt=2 |
|    This American Life     | 917918570  | 2018-11-15  |          Serial          | podcast |    © Copyright 2018 Serial Podcast     | 364380278  |          NA           | https://itunes.apple.com/us/artist/this-american-life/364380278?mt=2 | https://is4-ssl.mzstatic.com/image/thumb/Music71/v4/61/59/94/615994ff-21b5-9817-3e89-09b7e012336d/source/200x200bb.png  |                                                                                                   list(genreId = c("1311", "26"), name = c("News & Politics", "Podcasts"), url = c("https://itunes.apple.com/us/genre/id1311", "https://itunes.apple.com/us/genre/id26"))                                                                                                   |          https://itunes.apple.com/us/podcast/serial/id917918570?mt=2          |
|      Barstool Sports      | 1418960261 | 2018-12-12  |      Call Her Daddy      | podcast |         © 2018 Barstool Sports         | 973114159  |          NA           |  https://itunes.apple.com/us/artist/barstool-sports/973114159?mt=2   | https://is4-ssl.mzstatic.com/image/thumb/Music128/v4/6a/d2/a8/6ad2a82c-b005-c098-e0f3-9cf42bd58872/source/200x200bb.png |                                                                                                       list(genreId = c("1303", "26"), name = c("Comedy", "Podcasts"), url = c("https://itunes.apple.com/us/genre/id1303", "https://itunes.apple.com/us/genre/id26"))                                                                                                        |     https://itunes.apple.com/us/podcast/call-her-daddy/id1418960261?mt=2      |
|      HEAVY D SPARKS       | 1446780975 | 2018-12-16  |     HEAVY CHECKLIST      | podcast |         © All rights reserved          |     NA     |          NA           |                                  NA                                  | https://is3-ssl.mzstatic.com/image/thumb/Music128/v4/d5/64/f9/d564f9cb-79ba-7bb7-84bf-adc9b199c037/source/200x200bb.png |                                                                                                      list(genreId = c("1321", "26"), name = c("Business", "Podcasts"), url = c("https://itunes.apple.com/us/genre/id1321", "https://itunes.apple.com/us/genre/id26"))                                                                                                       |     https://itunes.apple.com/us/podcast/heavy-checklist/id1446780975?mt=2     |

</div>

## Podcast RSS Feeds from Podcast List

Now that we have the list of podcasts, we need to get the RSS feed for each one.  To do this, we can use the [iTunes API](https://affiliate.itunes.apple.com/resources/documentation/itunes-store-web-service-search-api/) by using the id found in the url.


{% highlight r %}
## Extract id
extractId <- function(appleLink) {
  id <- regmatches(appleLink, regexpr("(id\\d+)", appleLink)) # probably way to do this with gsub
  id <- gsub("id", "", id)
  return(id)
}

## Lookup by id
findRssById <- function(appleId) {
  apRes <- fromJSON(paste0("https://itunes.apple.com/lookup?entity=podcast&id=",appleId))
  return(apRes$results$feedUrl[1])
}
{% endhighlight %}

Now, we can extract the id and query the iTunes lookup and it will return the RSS feed of the podcast.  To do this, we loop over the podcast list and create a new dataframe with a bit of info.


{% highlight r %}
df <- NULL
for (k in 1:nrow(podcastList)) {
  df <- rbind(df, data.frame(podcast=podcastList$name[k], artist=podcastList$artistName[k], feed=findRssById(extractId(podcastList$url[k]))))
  Sys.sleep(0.5)
}
{% endhighlight %}

This will give us the RSS feeds for each of the top 100 podcasts found on iTunes.


{% highlight r %}
knitr::kable(df, align="c")
{% endhighlight %}

<div class="table-wrapper" markdown="block">


|                            podcast                            |                       artist                        |                                                                               feed                                                                                |
|:-------------------------------------------------------------:|:---------------------------------------------------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|                           Dr. Death                           |                       Wondery                       |                                                                  https://rss.art19.com/dr-death                                                                   |
|                          Dirty John                           |              L.A. Times &#124; Wondery              |                                                                 https://rss.art19.com/dirty-john                                                                  |
|                   The Joe Rogan Experience                    |                      Joe Rogan                      |                                                           http://joeroganexp.joerogan.libsynpro.com/rss                                                           |
|                            Serial                             |                 This American Life                  |                                                           http://feeds.serialpodcast.org/serialpodcast                                                            |
|                        Call Her Daddy                         |                   Barstool Sports                   |                                                     https://mcsorleys.barstoolsports.com/feed/call-her-daddy                                                      |
|                        HEAVY CHECKLIST                        |                   HEAVY D SPARKS                    |                                               http://feeds.soundcloud.com/users/soundcloud:users:590156/sounds.rss                                                |
|                            Bag Man                            |                MSNBC, Rachel Maddow                 |                                           http://podcastfeeds.nbcnews.com/drone/api/query/audio/podcast/1.0/bagman.xml                                            |
|                         Broken Harts                          |                iHeartRadio & Glamour                |                                                             https://feeds.megaphone.fm/HSW2732644812                                                              |
|               Armchair Expert with Dax Shepard                |                  Armchair Umbrella                  |                                                           https://rss.simplecast.com/podcasts/4123/rss                                                            |
|                      Phil in the Blanks                       |                   Dr. Phil McGraw                   |                                                             https://rss.art19.com/phil-in-the-blanks                                                              |
|                           The Daily                           |                 The New York Times                  |                                                                  http://rss.art19.com/the-daily                                                                   |
|                      This American Life                       |                 This American Life                  |                                                            http://feed.thisamericanlife.org/talpodcast                                                            |
|                        Up and Vanished                        |                    Tenderfoot TV                    |                                                               https://rss.art19.com/up-and-vanished                                                               |
|                          Happy Face                           |                     iHeartRadio                     |                                                               https://feeds.megaphone.fm/happyface                                                                |
| My Favorite Murder with Karen Kilgariff and Georgia Hardstark | Exactly Right / Karen Kilgariff & Georgia Hardstark |                                      https://rss.art19.com/my-favorite-murder-with-karen-kilgariff-and-georgia-hardstark-fb                                       |
|                        TED Talks Daily                        |                         TED                         |                                                            http://feeds.feedburner.com/TEDTalks_audio                                                             |
|                     Stuff You Should Know                     |             iHeartRadio & HowStuffWorks             |                                                           https://feeds.megaphone.fm/stuffyoushouldknow                                                           |
|                          In the Dark                          |                     APM Reports                     |                                                 https://feeds.publicradio.org/public_feeds/in-the-dark/itunes/rss                                                 |
|                           Slow Burn                           |                        Slate                        |                                                                http://feeds.megaphone.fm/watergate                                                                |
|                             Cold                              |                    KSL Podcasts                     |                                                                https://coldpodcast.libsyn.com/rss                                                                 |
|                           Caliphate                           |                 The New York Times                  |                                                                  https://rss.art19.com/caliphate                                                                  |
|                       The Teacher's Pet                       |                   The Australian                    |                                                           https://rss.whooshkaa.com/rss/podcast/id/3289                                                           |
|                   Duolingo Spanish Podcast                    |                      Duolingo                       |                                                         http://spanishpodcast.duolingo.libsynpro.com/rss                                                          |
|                         Hidden Brain                          |                         NPR                         |                                                           https://www.npr.org/rss/podcast.php?id=510308                                                           |
|                 Conan O’Brien Needs A Friend                  |                 Team Coco & Earwolf                 |                                                                https://rss.art19.com/conan-obrien                                                                 |
|                      Revisionist History                      |             Malcolm Gladwell / Pushkin              |                                                           https://feeds.megaphone.fm/revisionisthistory                                                           |
|                           Believed                            |                         NPR                         |                                                           https://www.npr.org/rss/podcast.php?id=510326                                                           |
|          LeBatard and Friends - South Beach Sessions          |             ESPN, Dan LeBatard, Stugotz             |                                                 http://www.espn.com/espnradio/feeds/rss/podcast.xml/_/id/25513139                                                 |
|                        TED Radio Hour                         |                         NPR                         |                                                           https://www.npr.org/rss/podcast.php?id=510298                                                           |
|                     The Dave Ramsey Show                      |                  Ramsey Solutions                   |                                                            http://daveramsey.ramsey.libsynpro.com/rss                                                             |
|                 Dan Carlin's Hardcore History                 |                     Dan Carlin                      |                                                     http://feeds.feedburner.com/dancarlin/history?format=xml                                                      |
|                          Bear Brook                           |             New Hampshire Public Radio              |                                                               https://feeds.megaphone.fm/bearbrook                                                                |
|                           The Dream                           |            Little Everywhere & Stitcher             |                                                                  https://rss.art19.com/the-dream                                                                  |
|                            S-Town                             |             Serial & This American Life             |                                                            http://feeds.stownpodcast.org/stownpodcast                                                             |
|                 How I Built This with Guy Raz                 |                         NPR                         |                                                           https://www.npr.org/rss/podcast.php?id=510313                                                           |
|                   Something You Should Know                   |          Mike Carruthers / OmniCast Media           |                                                          https://rss.art19.com/something-you-should-know                                                          |
|                      Freakonomics Radio                       |           Dubner Productions and Stitcher           |                                                             https://rss.art19.com/freakonomics-radio                                                              |
|                     The Ben Shapiro Show                      |                   The Daily Wire                    |                                              http://feeds.soundcloud.com/users/soundcloud:users:174770374/sounds.rss                                              |
|                           Radiolab                            |                    WNYC Studios                     |                                                                  http://feeds.wnyc.org/radiolab                                                                   |
|                  This Podcast Will Kill You                   |  Exactly Right / Erin Welsh & Erin Allmann Updyke   |                                                         https://rss.art19.com/this-podcast-will-kill-you                                                          |
|                         Kickass News                          |             Mathis Entertainment, Inc.              |                                                    https://kickasspoliticspreview1.libsyn.com/kickasspolitics2                                                    |
|                         Planet Money                          |                         NPR                         |                                                           https://www.npr.org/rss/podcast.php?id=510289                                                           |
|                           Mythology                           |                   Parcast Network                   |                                                               https://feeds.megaphone.fm/mythology                                                                |
|                  Wait Wait...Don't Tell Me!                   |                         NPR                         |                                                         https://www.npr.org/rss/podcast.php?id=344098539                                                          |
|                           The Corp                            |                   Barstool Sports                   |                                                      https://mcsorleys.barstoolsports.com/feed/barstool-corp                                                      |
|                      A Very Fatal Murder                      |                      The Onion                      |                                                             http://feeds.megaphone.fm/veryfatalmurder                                                             |
|               Sunday Sitdown with Willie Geist                |             Willie Geist, Sunday TODAY              |                              http://podcastfeeds.nbcnews.com/drone/api/query/audio/podcast/1.0/sunday-sitdown-with-willie-geist.xml                               |
|          Dark Poutine - True Crime and Dark History           |             Dark Poutine / Curiouscast              | https://www.omnycontent.com/d/playlist/fdc2ad13-d199-4e97-b2db-a59300cb6cc2/e03d4e1e-d40b-4706-82d2-a95f010d0639/4f01b694-96f6-408f-9d02-a95f010d064d/podcast.rss |
|                Oprah’s SuperSoul Conversations                |                        Oprah                        |                                                        https://rss.art19.com/oprah-supersoul-conversations                                                        |
|                        Sword and Scale                        |        Wondery &#124; Incongruity True Crime        |                                                               https://rss.art19.com/sword-and-scale                                                               |
|                       Pod Save America                        |                    Crooked Media                    |                                                           http://feeds.feedburner.com/pod-save-america                                                            |
|         Gladiator: Aaron Hernandez and Football Inc.          |           The Boston Globe &#124; Wondery           |                                                 https://rss.art19.com/gladiator-aaron-hernandez-and-football-inc                                                  |
|                           Up First                            |                         NPR                         |                                                           https://www.npr.org/rss/podcast.php?id=510318                                                           |
|                           Fresh Air                           |                         NPR                         |                                                         https://www.npr.org/rss/podcast.php?id=381444908                                                          |
|                         99% Invisible                         |                     Roman Mars                      |                                                      http://feeds.99percentinvisible.org/99percentinvisible                                                       |
|                           The Moth                            |                      The Moth                       |                                                            http://feeds.feedburner.com/themothpodcast                                                             |
|         The Daily Show With Trevor Noah: Ears Edition         |                   Comedy Central                    |                                                             https://feeds.megaphone.fm/the-daily-show                                                             |
|                         RISE podcast                          |                    Rachel Hollis                    |                                              http://feeds.soundcloud.com/users/soundcloud:users:300950004/sounds.rss                                              |
|               Stuff You Missed in History Class               |             iHeartRadio & HowStuffWorks             |                                                      https://feeds.megaphone.fm/stuffyoumissedinhistoryclass                                                      |
|                   Wolverine: The Long Night                   |                 Marvel and Stitcher                 |                                                          https://rss.art19.com/wolverine-the-long-night                                                           |
|               Oprah’s Master Class: The Podcast               |                        Oprah                        |                                                                https://rss.art19.com/master-class                                                                 |
|            VIEWS with David Dobrik and Jason Nash             |                      Cadence13                      |                                                             http://feeds.feedburner.com/views-podcast                                                             |
|                          Ear Hustle                           |               Ear Hustle & Radiotopia               |                                                             http://feeds.earhustlesq.com/earhustlesq                                                              |
|                             Lore                              |                    Aaron Mahnke                     |                                                                 http://lorepodcast.libsyn.com/rss                                                                 |
|                     The Tim Ferriss Show                      |  Tim Ferriss: Bestselling Author, Human Guinea Pig  |                                                              https://rss.art19.com/tim-ferriss-show                                                               |
|                       Art of the Hustle                       |                     iHeartRadio                     |                                                              https://rss.art19.com/art-of-the-hustle                                                              |
|                   The Tony Robbins Podcast                    |                    Tony Robbins                     |                                                                https://tonyrobbins.libsyn.com/rss                                                                 |
|                          Murderville                          |                    The Intercept                    |                                                     https://rss.prod.firstlook.media/murderville/podcast.rss                                                      |
|                    Someone Knows Something                    |                    CBC Podcasts                     |                                                          https://www.cbc.ca/podcasting/includes/sks.xml                                                           |
|                    The Numberphile Podcast                    |                     Brady Haran                     |                                                          https://www.numberphile.com/podcast?format=rss                                                           |
|                         Imagined Life                         |                       Wondery                       |                                                                https://rss.art19.com/imagined-life                                                                |
|                           Criminal                            |                Criminal & Radiotopia                |                                                           http://feeds.thisiscriminal.com/CriminalShow                                                            |
|                         Post Reports                          |                 The Washington Post                 |                                                        https://podcast.posttv.com/itunes/post-reports.xml                                                         |
|            Why Won't You Date Me? with Nicole Byer            |                       HeadGum                       |                                                            https://rss.art19.com/why-wont-you-date-me                                                             |
|           Where Should We Begin? with Esther Perel            |                       Audible                       |                                                              http://esther.audible.libsynpro.com/rss                                                              |
|                    Monday Morning Podcast                     |                      Bill Burr                      |                                                                  http://billburr.libsyn.com/rss                                                                   |
|                   Last Podcast On The Left                    |              The Last Podcast Network               |                                                                    http://lpotl.libsyn.com/rss                                                                    |
|                   Waking Up with Sam Harris                   |                     Sam Harris                      |                                                                  http://wakingup.libsyn.com/rss                                                                   |
|                           Aria Code                           |            WQXR & The Metropolitan Opera            |                                                                  http://feeds.wnyc.org/aria-code                                                                  |
|                         Jocko Podcast                         |                Jocko DEFCOR Network                 |                                                                http://jockopodcast.libsyn.com/rss                                                                 |
|                      Everything is Alive                      |                     Radiotopia                      |                                                       http://feeds.everythingisalive.com/everythingisalive                                                        |
|                  Impaulsive with Logan Paul                   |                     Logan Paul                      |                                                              https://feeds.megaphone.fm/KM3115607035                                                              |
|                       American Scandal                        |                       Wondery                       |                                                              https://rss.art19.com/american-scandal                                                               |
|                      Conspiracy Theories                      |                   Parcast Network                   |                                                             https://feeds.megaphone.fm/END5064265457                                                              |
|                    The Rachel Maddow Show                     |                Rachel Maddow, MSNBC                 |                                  http://podcastfeeds.nbcnews.com/drone/api/query/audio/podcast/1.0/MSNBC-MADDOW-NETCAST-MP3.xml                                   |
|         Pretty Basic with Alisha Marie and Remi Cruz          |                       Ramble                        |                                                             http://feeds.feedburner.com/pretty-basic                                                              |
|                   Woolie Will Figure It Out                   |                    Woolie Versus                    |                                                              https://woolievspodcast.libsyn.com/rss                                                               |
|                          Trump, Inc.                          |                    WNYC Studios                     |                                                                  http://feeds.wnyc.org/trumpinc                                                                   |
|                          Invisibilia                          |                         NPR                         |                                                           https://www.npr.org/rss/podcast.php?id=510307                                                           |
|                      Casefile True Crime                      |                 Casefile True Crime                 |                                                            https://audioboom.com/channels/4940872.rss                                                             |
|                         Hell and Gone                         |                     iHeartRadio                     |                                                             https://feeds.megaphone.fm/HSW5422627364                                                              |
|                       Business & Biceps                       |     Maurice Clarett, Cory Gregory & John Fosco      |                                                              http://businessandbiceps.libsyn.com/rss                                                              |
|                        Pardon My Take                         |                   Barstool Sports                   |                                                     https://mcsorleys.barstoolsports.com/feed/pardon-my-take                                                      |
|                The Jordan B. Peterson Podcast                 |   Dr. Jordan B. Peterson: Professor of Psychology   |                                                        https://feeds.blubrry.com/feeds/jordanbpeterson.xml                                                        |
|                     RISE Together Podcast                     |             Rachel Hollis & Dave Hollis             |                                              http://feeds.soundcloud.com/users/soundcloud:users:473483010/sounds.rss                                              |
|      Broken Record with Malcolm Gladwell and Rick Rubin       |                 Pushkin Industries                  |                                                             https://feeds.megaphone.fm/broken-record                                                              |
|                     My Dad Wrote A Porno                      |                My Dad Wrote A Porno                 |                                                               http://rss.acast.com/mydadwroteaporno                                                               |
|                 The GaryVee Audio Experience                  |                   Gary Vaynerchuk                   |                                                            http://askgaryvee.garyvee.libsynpro.com/rss                                                            |
|                          Homecoming                           |                       Gimlet                        |                                                            http://feeds.gimletmedia.com/homecomingshow                                                            |
|                 On Being with Krista Tippett                  |                  On Being Studios                   |                                                             https://onbeing.org/podcasts/podcast.xml                                                              |

</div>