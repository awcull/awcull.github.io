---
title: "Decoding Hearthstone Deck Codes with R"
output: html_document
date: "2017-10-07"
layout: post
categories: [R, Hearthstone]
comments: true
---

# Introduction

A few months ago from writing, Hearthstone released a way to generate and read a code to make deck sharing easier.  Using the code, allows the Hearthstone client to populate the deck list quickly, allowing users to avoid the cumbersome task of finding the card and adding it to their deck.  This is a quick introduction on how to decode the list in R.

This will use the jsonlite package to deencode the string as well as fetch the card list from [HearthstoneJSON](https://hearthstonejson.com).  The rest will be parsing the resultant to form a the deck.  BMS package will be used to check encoding.

# The Deck

It's always easiest when working towards a known solution.  So the following deckcode and deck will be used:

 Secret Mage
 
 Class: Mage
 
 Format: Standard
 
 Year of the Mammoth

 
 2x (1) Mana Wyrm
 
 2x (2) Arcanologist
 
 2x (2) Frostbolt
 
 2x (2) Medivh's Valet
 
 2x (2) Primordial Glyph
 
 2x (3) Arcane Intellect
 
 2x (3) Counterspell
 
 1x (3) Ice Block
 
 2x (3) Kirin Tor Mage
 
 2x (3) Mirror Entity
 
 2x (3) Volcanic Potion
 
 2x (4) Fireball
 
 1x (4) Ghastly Conjurer
 
 2x (6) Kabal Crystal Runner
 
 1x (7) Bonemare
 
 2x (7) Firelands Portal
 
 1x (8) Medivh, the Guardian
 
AAECAf0EBMABobcC3s0Cps4CDXHDAbsClQOrBJYF7AWjtgLXtgLpugKHvQLBwQKYxAIA
 
 To use this deck, copy it to your clipboard and create a new deck in Hearthstone

# Decoding The String

The deck string is a base64 encoded compact byte string.  This can make it a bit harder to decode in R compared to other languages such as python or c/c++/c#.  For more information about the deck string [HearthSim has an excellent write up](https://hearthsim.info/docs/deckstrings/).  From this, the values are in *varint*, so will need be be decoded properly.

## DeEncode base64

The first step is to deencode the base64 byte string.  To do this, the jsonlite package will be used to do this.


{% highlight r %}
library(jsonlite)
library(BMS)

deckCode <- "AAECAf0EBMABobcC3s0Cps4CDXHDAbsClQOrBJYF7AWjtgLXtgLpugKHvQLBwQKYxAIA"
# decode string
deck.denc <- base64_dec(deckCode)

cat(deck.denc)
{% endhighlight %}



{% highlight text %}
## 00 01 02 01 fd 04 04 c0 01 a1 b7 02 de cd 02 a6 ce 02 0d 71 c3 01 bb 02 95 03 ab 04 96 05 ec 05 a3 b6 02 d7 b6 02 e9 ba 02 87 bd 02 c1 c1 02 98 c4 02 00
{% endhighlight %}
Now going over this, there is a header block followed by card blocks, and finally terminating the 0x00.  Since this is in base 16, let us set this.


{% highlight r %}
base <- 16L
{% endhighlight %}
Before we begin, we have to setup a function that will become helpful later on.  This is to convert the hex value in a given base to a number:


{% highlight r %}
# Returns a number based on hex input and a base
bin2dec <- function(x) {
    return(sum(2^(which(rev(unlist(strsplit(as.character(x), "")) == 1))-1)))
}

convToNum <- function(x) {
  return(bin2dec(hex2bin(as.character(x))))
}
{% endhighlight %}

Other functions will be needed later but for now, this gets us started.


## Header Block

The header block starts with 0x00, followed by the version, then the Hearthstone game format (Standard or Wild).  So, to decode this:


{% highlight r %}
base <- 16L
# Initial 3:
# Reserved byte 0x00
# Version (1)
# Format --- 2 = standard, 1 = wild
deck.ver <- convToNum(deck.denc[2])
deck.format <- convToNum(deck.denc[3])
{% endhighlight %}

This gives us a version of 1, and format 2 which is standard.

## Card Block

The cards are split into four blocks, starting with a length then the list.  The order goes:

- Heroes
- Single cards
- Double cards
- 3 or more cards

### Hero and Card Blocks

The first block starts at position 4 (R starts arrays at 1).  From this, we can convert it to a number and use this as the number of cards in the list for that block.  This list is varint values, which may cause an issue due to endianness.

Endianess of the system.  Currently, I'm using my Windows machine which can assume little endian.  Unfortunately, I'm not sure how to set the endianness in R to properly decode.  The last part is the card codes are stored as varint.  This means that the least or most signifigant bit must be check for a '1' indicating there is more values to read in for the number.  The values are swapped in order, concatenated and '0's removed from start.  More on varint can be found from [Google write up](https://developers.google.com/protocol-buffers/docs/encoding).



{% highlight r %}
createCardList <- function(vals, whichBase) {
  cards <- NULL
  for (k in seq(1, length(vals), 2)) {
    cards <- c(cards,convToNum(paste0(vals[k+1], vals[k], collapse=""), whichBase ))
  }
  return(cards)
}
{% endhighlight %}

Now we can grab the blocks.


{% highlight r %}
# Set up values
currentCardIter <- -1 # which block iteration
cardBlockStart <- 4 # First block
blockSize <- 0 # current block size
idx <- cardBlockStart # index over deck code
out.df <- NULL # output data frame holding which card block (0,1,2,3,...,n) and dbfid of card
cardList <- NULL # tmp array to hold card lists
isBlockStart <- TRUE # start or in a block
# Loop over deck code
while(idx < length(deck.denc)) {
  # If start of a block, reset values and get block size
  if (isBlockStart) {
    blockSize <- bin2dec(hex2bin(as.character(deck.denc[idx])))
    currValue <- NULL
    cardList <- NULL
    currentCardIter <- currentCardIter + 1
    isBlockStart <- FALSE
  } else {
    tmp <- hex2bin(as.character(deck.denc[idx]))
    currValue <- c(tmp[2:length(tmp)],currValue)
    # If 0, then end and calculate card
    if (tmp[1] == 0) {
      dbfid <- bin2dec(gsub("^0+([1-9])", "\\1",paste0(currValue, collapse="")))
      cardList <- c(cardList, dbfid)
      currValue <- NULL
      # If reached end of block, store values and reset
      if (length(cardList) == blockSize) {
        out.df <- rbind(out.df, data.frame(cardIter=rep(currentCardIter,blockSize), dbfid = cardList), stringsAsFactors=F)
        isBlockStart <- TRUE
      }
    }
  }
  idx <- idx + 1
}
{% endhighlight %}

The last part is now, taking this data and putting it together with the card database:


{% highlight r %}
urlCards <- "https://api.hearthstonejson.com/v1/latest/enUS/cards.collectible.json"
cardInfo <- fromJSON(urlCards)

# Match list
deck.df <- merge(out.df, cardInfo[,c("name", "dbfId", "cost")], by.x="dbfid", by.y= "dbfId")
knitr::kable(deck.df, align='c')
{% endhighlight %}



| dbfid | cardIter |         name         | cost |
|:-----:|:--------:|:--------------------:|:----:|
|  113  |    2     |     Counterspell     |  3   |
|  192  |    1     |      Ice Block       |  3   |
|  195  |    2     |    Mirror Entity     |  3   |
|  315  |    2     |       Fireball       |  4   |
|  405  |    2     |      Mana Wyrm       |  1   |
|  555  |    2     |   Arcane Intellect   |  3   |
|  637  |    0     |   Jaina Proudmoore   |  NA  |
|  662  |    2     |      Frostbolt       |  2   |
|  748  |    2     |    Kirin Tor Mage    |  3   |
| 39715 |    2     |   Firelands Portal   |  7   |
| 39767 |    2     |    Medivh's Valet    |  2   |
| 39841 |    1     | Medivh, the Guardian |  8   |
| 40297 |    2     |   Volcanic Potion    |  3   |
| 40583 |    2     | Kabal Crystal Runner |  6   |
| 41153 |    2     |     Arcanologist     |  2   |
| 41496 |    2     |   Primordial Glyph   |  2   |
| 42718 |    1     |   Ghastly Conjurer   |  4   |
| 42790 |    1     |       Bonemare       |  7   |

Whats left now is to sort this for final display:
 

{% highlight r %}
hero <- deck.df$name[is.na(deck.df$cost)]
deck.df <- deck.df[!is.na(deck.df$cost),]
deck.df <- deck.df[,c("cardIter", "name", "cost")]
names(deck.df) <- c("Card.Count", "Name", "Cost")
deck.df <- deck.df[,c("Card.Count", "Cost", "Name")]

cat(hero)
{% endhighlight %}



{% highlight r %}
knitr::kable(deck.df[order(deck.df$Cost,deck.df$Card.Count, deck.df$Name),], align='c', row.names=F)
{% endhighlight %}

## Jaina Proudmoore

| Card.Count | Cost |         Name         |
|:----------:|:----:|:--------------------:|
|     2      |  1   |      Mana Wyrm       |
|     2      |  2   |     Arcanologist     |
|     2      |  2   |      Frostbolt       |
|     2      |  2   |    Medivh's Valet    |
|     2      |  2   |   Primordial Glyph   |
|     1      |  3   |      Ice Block       |
|     2      |  3   |   Arcane Intellect   |
|     2      |  3   |     Counterspell     |
|     2      |  3   |    Kirin Tor Mage    |
|     2      |  3   |    Mirror Entity     |
|     2      |  3   |   Volcanic Potion    |
|     1      |  4   |   Ghastly Conjurer   |
|     2      |  4   |       Fireball       |
|     2      |  6   | Kabal Crystal Runner |
|     1      |  7   |       Bonemare       |
|     2      |  7   |   Firelands Portal   |
|     1      |  8   | Medivh, the Guardian |

Whats left would be to add in enum types for the heros and game format.  But this is enough to get what I originally had wanted.
