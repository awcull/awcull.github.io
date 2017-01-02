---
title: "Budweiser Prices in Canada"
date: "2017-01-02"
output: html_document
comments: true
layout: post
categories: [R, beer, Canada]
---

## Introduction

When privitization of liquor stores comes up, Alberta is always shown as an example of how cheap and easily obtainable it is there.  Although I can't speak to the availability of it, I decided to take a quick look into the price of a commonly available beer at each province in Canada.  To do this, a 24 can pack of Budweiser was chosen, and prices were obtained from retailer websites across Canada.  

## Data

Data was gathered from provincial liquor control website or from retailers website if not available.  




{% highlight r %}
b.avg.df <- ddply(b.df, .(Province), summarise, avg.Price=mean(Cost.cad))

ggplot(b.avg.df, aes(x=Province, y=avg.Price)) + geom_bar(stat="identity") + ggtitle("Cost of 24 cans Budweiser\n$CAD") + ylab("Price ($CAD)") + theme(axis.text.x = element_text(angle = 90, hjust=1), plot.title=element_text(hjust=0.5))
{% endhighlight %}

![]({{ site.url }}/assets/2017-01-02-beer-prices-quick/data-1.png){: .centerIMG} 

Original data:


{% highlight r %}
kable(b.df, align='c')
{% endhighlight %}



|          Province          |                                                                                   Link                                                                                   | Cost.cad |       Product       |    Date    |       Store       |
|:--------------------------:|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|:--------:|:-------------------:|:----------:|:-----------------:|
|           Qubec            |                                                                http://www.dcbeersales.ca/beer-prices.html                                                                |  29.99   | 24 Can of Budweiser | 02/01/2017 |                   |
|      British Columbia      |                                                               http://www.bcliquorstores.com/product/906354                                                               |  34.29   | 24 Can of Budweiser | 02/01/2017 |     BC Liquor     |
|        Saskachewan         | http://www.saskliquor.com/SaskLiquorWeb/ProductDetail.aspx?ID=1511&ref=L1Nhc2tMaXF1b3JXZWIvUHJvZHVjdFNlYXJjaFJlc3VsdHMuYXNweD9rZXl3b3Jkcz1idWR3ZWlzZXI%3d-mNH1D34amtE%3d |  42.99   | 24 Can of Budweiser | 02/01/2017 |    Sask Liquor    |
|          Manitoba          |                                                         http://www.liquormarts.ca/product/budweiser/24-x-355-ml                                                          |  40.31   | 24 Can of Budweiser | 02/01/2017 |       MLCC        |
|          Ontario           |                                                                http://www.thebeerstore.ca/beers/budweiser                                                                |  45.95   | 24 Can of Budweiser | 02/01/2017 |     BeerStore     |
|       New Bruinswick       |                                                         http://www.nbliquor.com/documents/Current_Price_List.pdf                                                         |  42.65   | 24 Can of Budweiser | 02/01/2017 |     NB Liquor     |
|        Nova Scotia         |                                                 https://www.mynslc.com/en/products/Beer/Lager/Pale%20Lager/1016378.aspx                                                  |  43.95   | 24 Can of Budweiser | 02/01/2017 |                   |
| Newfound Land and Labrador |                                                      http://www.nlliquor.com/products/16052/Budweiser%20Cans%2024pk                                                      |  52.13   | 24 Can of Budweiser | 02/01/2017 |    NLL Liquor     |
|    Prince Edward Island    |                                               http://liquorpei.com/wp-content/uploads/2016/07/December-2016-Price-List.pdf                                               |  45.39   | 24 Can of Budweiser | 02/01/2017 |    Liquor PEI     |
|           Yukon            |                                                http://www.ylc.yk.ca/pdf/Price_List_Yukon_Liquor_Corporation_July_2016.pdf                                                |  45.20   | 24 Can of Budweiser | 02/01/2017 |        YLC        |
|          Alberta           |                                                          http://www.liquordirect.ca/Budweiser-Beer-P10623.aspx                                                           |  47.99   | 24 Can of Budweiser | 02/01/2017 | Liquor Depot/Barn |
|          Alberta           |                                                                     http://liquor.sobeys.com/flyer/                                                                      |  47.99   | 24 Can of Budweiser | 02/01/2017 |      Sobeys       |
|   Northwest Territories    |                                                                                                                                                                          |    NA    |                     |            |                   |
|          Nunavut           |                                                                                                                                                                          |    NA    |                     |            |                   |
