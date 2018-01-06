---
title: "Hearthstone Cards with No Modifiers"
date: '2017-12-16'
output: html_document
comments: true
layout: post
categories: [R, hearthstone]
---



# Introduction

On December 7th, 2017, the Kobolds and Catacombs exapnsion for Hearthstone was released.  This includes 135 new cards added to the game.  With any collectible card game there are always filler cards, this will examine percentage of cards found in each expansion that contain no modifiers.  In Hearthstone, a modifier is any text written on the card that gives it some ability.  Examples of this are *battlecry* where the modifier is executed when played or *deathrattle* which triggers when the card is removed from the play field.

# Data

The data will be gathered from [HearthstoneJSON](https://hearthstonejson.com/) and parsed with jsonlite package.


{% highlight r %}
urlCards <- "https://api.hearthstonejson.com/v1/latest/enUS/cards.collectible.json"
cardInfo <- fromJSON(urlCards)
{% endhighlight %}

This data set has 33 columns, and will need to be trimmed down to keep only the set, class, and card name.


{% highlight r %}
ntCards <- cardInfo[,c("name","cardClass", "set", "text")]
ntCards <- ntCards[is.na(ntCards$text),]
# Fix a set here EXPERT1 to CORE
ntCards$set[ntCards$set == "EXPERT1"] <- "CORE"
{% endhighlight %}

This data set also contains hero cards that need to be removed.


{% highlight r %}
ntCards <- ntCards[ntCards$set != "HERO_SKINS",] # Skins
ntCards <- ntCards[ntCards$name != "Malfurion Stormrage",] # Druid
ntCards <- ntCards[ntCards$name != "Jaina Proudmoore",] # Mage
ntCards <- ntCards[ntCards$name != "Anduin Wrynn",] # Priest
ntCards <- ntCards[ntCards$name != "Rexxar",] # Hunter
ntCards <- ntCards[ntCards$name != "Uther Lightbringer",] # Paladin
ntCards <- ntCards[ntCards$name != "Valeera Sanguinar",] # Rogue
ntCards <- ntCards[ntCards$name != "Thrall",] # Shaman
ntCards <- ntCards[ntCards$name != "Garrosh Hellscream",] # Warrior
ntCards <- ntCards[ntCards$name != "Gul'dan",] # Warlock
{% endhighlight %}

Lets add in dates and number of cars of each expansion, this is gathered from [Wikipedia](https://en.wikipedia.org/wiki/Hearthstone_(video_game)).


{% highlight r %}
# Data taken on 2017-12-16
setData <- data.frame(setName=c("Core","Goblins vs Gnomes", "The Grand Tournament", "League of Explorers","Whispers of the Old Gods", "One Night in Karazhan","Mean Streets of Gadgetzan", "Journey to UnGoro", "Knights of the Frozen Throne", "Kobolds and Catacombs"),
                     hsjName=c("CORE","GVG", "TGT", "LOE", "OG", "KARA","GANGS", "UNGORO", "ICECROWN", "LOOTAPALOOZA"),
                     releaseDate=c("2014-03-11","2014-12-08", "2015-08-24", "2015-11-12","2016-04-26", "2016-08-11","2016-12-01", "2017-04-06", "2017-08-10", "2017-12-08"),
                     numCards=c(239,123, 132, 45, 134, 45, 132, 135,135,135), stringsAsFactors=F)
setData$releaseDate <- as.Date(setData$releaseDate)
# Use dplyr inner join
ntCards <- inner_join(ntCards, setData, by=c("set" = "hsjName")) %>% arrange(releaseDate, name)

knitr::kable(ntCards, align="c")
{% endhighlight %}



|        name         | cardClass |     set      | text |           setName            | releaseDate | numCards |
|:-------------------:|:---------:|:------------:|:----:|:----------------------------:|:-----------:|:--------:|
|   Arcanite Reaper   |  WARRIOR  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|  Assassin's Blade   |   ROGUE   |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|   Bloodfen Raptor   |  NEUTRAL  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|  Boulderfist Ogre   |  NEUTRAL  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|   Chillwind Yeti    |  NEUTRAL  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|     Core Hound      |  NEUTRAL  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|    Fiery War Axe    |  WARRIOR  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|   Light's Justice   |  PALADIN  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|     Magma Rager     |  NEUTRAL  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|    Murloc Raider    |  NEUTRAL  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|    Oasis Snapjaw    |  NEUTRAL  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|   River Crocolisk   |  NEUTRAL  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|      War Golem      |  NEUTRAL  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|        Wisp         |  NEUTRAL  |     CORE     |  NA  |             Core             | 2014-03-11  |   239    |
|  Lost Tallstrider   |  NEUTRAL  |     GVG      |  NA  |      Goblins vs Gnomes       | 2014-12-08  |   123    |
|    Puddlestomper    |  NEUTRAL  |     GVG      |  NA  |      Goblins vs Gnomes       | 2014-12-08  |   123    |
|      Salty Dog      |  NEUTRAL  |     GVG      |  NA  |      Goblins vs Gnomes       | 2014-12-08  |   123    |
|     Spider Tank     |  NEUTRAL  |     GVG      |  NA  |      Goblins vs Gnomes       | 2014-12-08  |   123    |
| Captured Jormungar  |  NEUTRAL  |     TGT      |  NA  |     The Grand Tournament     | 2015-08-24  |   132    |
| Fearsome Doomguard  |  WARLOCK  |     TGT      |  NA  |     The Grand Tournament     | 2015-08-24  |   132    |
|      Ice Rager      |  NEUTRAL  |     TGT      |  NA  |     The Grand Tournament     | 2015-08-24  |   132    |
|     Pit Fighter     |  NEUTRAL  |     TGT      |  NA  |     The Grand Tournament     | 2015-08-24  |   132    |
|   Murloc Tinyfin    |  NEUTRAL  |     LOE      |  NA  |     League of Explorers      | 2015-11-12  |    45    |
|    Am'gam Rager     |  NEUTRAL  |      OG      |  NA  |   Whispers of the Old Gods   | 2016-04-26  |   134    |
|    Carrion Grub     |  HUNTER   |      OG      |  NA  |   Whispers of the Old Gods   | 2016-04-26  |   134    |
|      Duskboar       |  NEUTRAL  |      OG      |  NA  |   Whispers of the Old Gods   | 2016-04-26  |   134    |
|   Eldritch Horror   |  NEUTRAL  |      OG      |  NA  |   Whispers of the Old Gods   | 2016-04-26  |   134    |
|  Faceless Behemoth  |  NEUTRAL  |      OG      |  NA  |   Whispers of the Old Gods   | 2016-04-26  |   134    |
|   Enchanted Raven   |   DRUID   |     KARA     |  NA  |    One Night in Karazhan     | 2016-08-11  |    45    |
|   Worgen Greaser    |  NEUTRAL  |    GANGS     |  NA  |  Mean Streets of Gadgetzan   | 2016-12-01  |   132    |
|      Ultrasaur      |  NEUTRAL  |    UNGORO    |  NA  |      Journey to UnGoro       | 2017-04-06  |   135    |
| Snowflipper Penguin |  NEUTRAL  |   ICECROWN   |  NA  | Knights of the Frozen Throne | 2017-08-10  |   135    |
|      Dire Mole      |  NEUTRAL  | LOOTAPALOOZA |  NA  |    Kobolds and Catacombs     | 2017-12-08  |   135    |

A quick glance shows that as newer sets are being released, cards without a modifier have much rarer compared to the base and early expansions.


{% highlight r %}
cardTable <- ntCards %>% 
  group_by(setName) %>% 
  summarise(count.of.no.modifier=n(), total.cards.set=numCards[1], percent.of.cards=round(n()/numCards[1] * 100,2), release=releaseDate[1]) %>% arrange(release)

knitr::kable(cardTable, align="c")
{% endhighlight %}



|           setName            | count.of.no.modifier | total.cards.set | percent.of.cards |  release   |
|:----------------------------:|:--------------------:|:---------------:|:----------------:|:----------:|
|             Core             |          14          |       239       |       5.86       | 2014-03-11 |
|      Goblins vs Gnomes       |          4           |       123       |       3.25       | 2014-12-08 |
|     The Grand Tournament     |          4           |       132       |       3.03       | 2015-08-24 |
|     League of Explorers      |          1           |       45        |       2.22       | 2015-11-12 |
|   Whispers of the Old Gods   |          5           |       134       |       3.73       | 2016-04-26 |
|    One Night in Karazhan     |          1           |       45        |       2.22       | 2016-08-11 |
|  Mean Streets of Gadgetzan   |          1           |       132       |       0.76       | 2016-12-01 |
|      Journey to UnGoro       |          1           |       135       |       0.74       | 2017-04-06 |
| Knights of the Frozen Throne |          1           |       135       |       0.74       | 2017-08-10 |
|    Kobolds and Catacombs     |          1           |       135       |       0.74       | 2017-12-08 |
