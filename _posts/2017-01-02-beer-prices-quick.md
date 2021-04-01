---
title: "Budweiser Prices in Canada"
date: "2017-01-02"
output: html_document
comments: true
layout: post
categories: [R, beer, Canada]
---

**UPDATE: [An updated list for 15 x 355 mL Bud Light can be found by clicking here.](https://awcull.com/2021/03/29/budlight-canada-prices.html)**

## Introduction

When privitization of liquor stores comes up, Alberta is always shown as an example of how cheap and easily obtainable it is there.  Although I can't speak to the availability of it, I decided to take a quick look into the price of a commonly available beer at each province in Canada.  To do this, a 24 can pack of Budweiser was chosen, and prices were obtained from retailer websites across Canada.  

## Data

Data was gathered from provincial liquor control website or from retailers website if not available.  




{% highlight r %}
b.avg.df <- ddply(b.df, .(Province), summarise, avg.Price=mean(Cost.cad))

ggplot(b.avg.df, aes(x=Province, y=avg.Price)) + geom_bar(stat="identity") + ggtitle("Cost of 24 cans Budweiser\n$CAD") + ylab("Price ($CAD)") + xlab("Province or Territory") + theme(axis.text.x = element_text(angle = 90, hjust=1), plot.title=element_text(hjust=0.5))
{% endhighlight %}

![]({{ site.url }}/assets/2017-01-02-beer-prices-quick/data-1.png){: .centerIMG} 

Original data:


{% highlight r %}
kable(b.df, align='c')
{% endhighlight %}

<div class="table-wrapper" markdown="block">

|          Province          | Cost.cad |       Product       |    Date    |       Store       |                                                                                   Link                                                                                   |
|:--------------------------:|:--------:|:-------------------:|:----------:|:-----------------:|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|           Qubec            |  29.99   | 24 Can of Budweiser | 02/01/2017 |                   |                                                                [Link](http://www.dcbeersales.ca/beer-prices.html)                                                                |
|      British Columbia      |  34.29   | 24 Can of Budweiser | 02/01/2017 |     BC Liquor     |                                                               [Link](http://www.bcliquorstores.com/product/906354)                                                               |
|        Saskachewan         |  42.99   | 24 Can of Budweiser | 02/01/2017 |    Sask Liquor    | [Link](http://www.saskliquor.com/SaskLiquorWeb/ProductDetail.aspx?ID=1511&ref=L1Nhc2tMaXF1b3JXZWIvUHJvZHVjdFNlYXJjaFJlc3VsdHMuYXNweD9rZXl3b3Jkcz1idWR3ZWlzZXI%3d-mNH1D34amtE%3d) |
|          Manitoba          |  40.31   | 24 Can of Budweiser | 02/01/2017 |       MLCC        |                                                         [Link](http://www.liquormarts.ca/product/budweiser/24-x-355-ml)                                                          |
|          Ontario           |  45.95   | 24 Can of Budweiser | 02/01/2017 |     BeerStore     |                                                                [Link](http://www.thebeerstore.ca/beers/budweiser)                                                                |
|       New Bruinswick       |  42.65   | 24 Can of Budweiser | 02/01/2017 |     NB Liquor     |                                                         [Link](http://www.nbliquor.com/documents/Current_Price_List.pdf)                                                         |
|        Nova Scotia         |  43.95   | 24 Can of Budweiser | 02/01/2017 |       NSLC        |                                                 [Link](https://www.mynslc.com/en/products/Beer/Lager/Pale%20Lager/1016378.aspx)                                                  |
| Newfound Land and Labrador |  52.13   | 24 Can of Budweiser | 02/01/2017 |    NLL Liquor     |                                                      [Link](http://www.nlliquor.com/products/16052/Budweiser%20Cans%2024pk)                                                      |
|    Prince Edward Island    |  45.39   | 24 Can of Budweiser | 02/01/2017 |    Liquor PEI     |                                               [Link](http://liquorpei.com/wp-content/uploads/2016/07/December-2016-Price-List.pdf)                                               |
|           Yukon            |  45.20   | 24 Can of Budweiser | 02/01/2017 |        YLC        |                                                [Link](http://www.ylc.yk.ca/pdf/Price_List_Yukon_Liquor_Corporation_July_2016.pdf)                                                |
|          Alberta           |  47.99   | 24 Can of Budweiser | 02/01/2017 | Liquor Depot/Barn |                                                          [Link](http://www.liquordirect.ca/Budweiser-Beer-P10623.aspx)                                                           |
|          Alberta           |  47.99   | 24 Can of Budweiser | 02/01/2017 |      Sobeys       |                                                                     [Link](http://liquor.sobeys.com/flyer/)                                                                      |
|   Northwest Territories    |    NA    |                     |            |                   |                                                                                                                                                                          |
|          Nunavut           |    NA    |                     |            |                   |                                                                                                                                                                          |

</div>

**Edit (2018-12-26)** Updated links to be clickable to reduce space in table, instead of full url
