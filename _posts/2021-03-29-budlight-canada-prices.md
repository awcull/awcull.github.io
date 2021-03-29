---
title: "Bud Light Beer (15x355ml) Prices in Canada"
author: "Andrew Cull"
date: "2021-03-29"
layout: post
categories: [R, beer, canada, prices, bud light]
comments: true
output: html_document
---
# Bud Light Canadian Beer Prices

Follow up onto a previous [post](https://www.awcull.com/2017/01/02/beer-prices-quick.html). The choice of 15 x 355 mL was chosen as 8 and 15 pack cans have been appearing more and more (anecdotally) and wanted to see the prices of a readily available beer across Canada.

![]({{ site.url }}/assets/2021-03-29-budlight-canada-prices/plot-1.png){: .centerIMG}

# Data 

Data for 15 x 355 mL Bud Light Cans: 

<style>
tr:nth-child(even) {
  background-color: #e8e8e8;
}
table {
  border: 1px solid #ddd;
}
</style>


<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Province </th>
   <th style="text-align:right;"> Price (CDN$) </th>
   <th style="text-align:left;"> Link </th>
   <th style="text-align:left;"> Date </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Saskatchewan </td>
   <td style="text-align:right;"> 35.49 </td>
   <td style="text-align:left;"> <a href="https://www.saskliquor.com/beer/m1665210/labatt-breweries-15c-bud-light-5325ml/">Link</a> </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Alberta </td>
   <td style="text-align:right;"> 31.99 </td>
   <td style="text-align:left;"> <a href="https://www.willowpark.net/collections/beer/products/bud-light-cans">Link</a>, <a href="https://shop.realcanadianliquorstore.ca/products/bud-light?variant=39305880174754">Link</a> </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Nova Scotia </td>
   <td style="text-align:right;"> 30.49 </td>
   <td style="text-align:left;"> <a href="https://www.mynslc.com/en/products/Beer/Lager/Light%20Lager/1006324.aspx">Link</a> </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Prince Edward Island </td>
   <td style="text-align:right;"> 30.49 </td>
   <td style="text-align:left;"> <a href="https://liquorpei.com/products/bud-light/">Link</a> </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Ontario </td>
   <td style="text-align:right;"> 29.95 </td>
   <td style="text-align:left;"> <a href="https://www.thebeerstore.ca/beers/bud-light/">Link</a> </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> New Brunswick </td>
   <td style="text-align:right;"> 29.49 </td>
   <td style="text-align:left;"> <a href="https://www.anbl.com/bud-light-15-c-17124">Link</a> </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Yukon </td>
   <td style="text-align:right;"> 29.00 </td>
   <td style="text-align:left;"> <a href="https://yukon.ca/sites/yukon.ca/files/ylc/ylc-liquor-retail-price-list-sept-2020.pdf">Link</a> </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Manitoba </td>
   <td style="text-align:right;"> 27.98 </td>
   <td style="text-align:left;"> <a href="https://www.liquormarts.ca/product/bud-light/15-x-355-ml">Link</a> </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> British Columbia </td>
   <td style="text-align:right;"> 25.49 </td>
   <td style="text-align:left;"> <a href="https://www.bcliquorstores.com/product/683847">Link</a> </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Quebec </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Newfoundland and Labrador </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Nunavut </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Northwest Territories </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 2021-03-24 </td>
  </tr>
</tbody>
</table>
