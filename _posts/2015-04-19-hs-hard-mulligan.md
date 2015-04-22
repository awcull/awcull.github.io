---
layout: post
title:  "Probability of Mulliganing for a Card in Hearthstone"
date:   2015-04-19 22:17:23
categories: hearthstone
---

# Introduction

[Hearthstone](http://us.battle.net/hearthstone/en/) is a free to play online card game.  The basics of it involve one of 9 classes with a 30 card deck with a max of two unique cards except for a legend cards which have a max of one.  At the start of each game, a player draws 3 or 4 cards depending who plays first.  A player can either keep the cards dealt or mulligan any of the dealt cards and draw the same amount back.  If a player chooses to mulligan a card, the card is removed from the deck and reshuffled back in, after the mulligan phase is complete.   After this point, cards are drawn one at a time at the start of each turn.  As well as drawing a card, a player gains one more mana crystal that can be spent to play cards that turn, up to a maximum of 10 crystals.  The game continues until a player reaches 0 life from the started 30.

In Hearthstone there are certain decks that benefit greatly from having at least 1 of the cards in the opening hand.  In the past this was [Undertaker](http://hearthstone.gamepedia.com/Undertaker) played in a deathrattle hunter deck.  Before the nerf, this card could gain attack and health very quickly as the player played more deathrattle minions to buff it up.  In recent times, the [Mechwarper](http://hearthstone.gamepedia.com/Mechwarper) which allows other cards of the mech type to cost 1 less have become popular.  This effectively ramps the mech player to more expensive cards quicker.  On the flip side, a warrior class player may want to mulligan hard for a [Fiery War Axe](http://hearthstone.gamepedia.com/Fiery_War_Axe) to be able to remove early high priority minions, such as the Mechwarper.  

# Probability

For this, it is assumed that the player has two copies of the card in the deck.  To calculate the probability of drawing a number of cards and getting a specific card can be done using combinations.  To do this, calculating the probability of NOT drawing the card is calculated such that:  P(*drawing card*) = 1 - P(*NOT drawing the card*).

## First Player - 3 Card Mulligan

### Card in First 3

The probability of getting the card is in the first 3 becomes:

P(*Card in First 3*) = 1 - P(*Card NOT in opening*) = 1 - # of combinations / # of total combinations

{% highlight r %}
1 - choose(27, 2)/choose(30, 2)
{% endhighlight %}

```
## [1] 0.1931
```


### Card After Mulligan

From this, calculating the the probability of getting the card in the second set, assuming not keeping any of the original 3:

P(*Card after Mulligan*) = 1 - (P(*Card NOT in First 3*) * P(*Card NOT After First 3*))

{% highlight r %}
1 - (choose(27, 2)/choose(30, 2)) * (choose(24, 2)/choose(27, 2))
{% endhighlight %}

```
## [1] 0.3655
```


Since the original three cards can not be drawn in the mulligan, it reduces the total number of cards to 27.

### Card After Mulligan AND first draw

With only one card being drawn, this becomes:

P(*Card after Mulligan AND First Draw*) = 1 - (P(*Card NOT in First 3*) * P(*Card NOT in Mulligan*)) * P(*NOT drawing first*)

{% highlight r %}
1 - (choose(27, 2)/choose(30, 2)) * (choose(24, 2)/choose(27, 2)) * (25/27)
{% endhighlight %}

```
## [1] 0.4125
```

The 27 comes from the mulliganed cards being re-shuffled back into the deck after the mulliganing phase is over.

## Second Player - 4 Card Mulligan
### Card after Mulligan AND first draw

Following the logic of the first player:

{% highlight r %}
1 - (choose(26, 2)/choose(30, 2)) * (choose(22, 2)/choose(26, 2)) * (24/26)
{% endhighlight %}

```
## [1] 0.5098
```


# Conclusion

From this, if a player hard mulligans for a card the probability of obtaining it are:

First Player:  41.3%

Second Player: 51.0%