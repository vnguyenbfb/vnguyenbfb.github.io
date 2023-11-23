---
title:  "Superstore Sales Analysis"
mathjax: true
layout: post
categories: media
---

<img style="float:left" src="/assets/images/Image8_small.png">

Superstore dataset is includes sales information of a small retail business named SuperStore. We will analyze the dataset and use Tableau to develop a story line for visualization and analysis.


<br> <br> <br> <br> <br> <br>


### Table of Content
   * Introduction
   * Analysis Direction
   * Analysis
   * Summary of Findings

### 1. Introduction
#### *1.1. The Dataset*
SuperStore dataset is a demonstrative dataset of a small retail business located in the United States which sells Furniture, Office Supplies and Technology products to their customers consisting of the mass Consumers, Corporates and Home Offices.

This dataset is from the series of “Data Visualization with Tableau” I took on Coursera. It can be found [here](/Full-Sales-Superstore-Dataset.xlsx).

The dataset contains comprehensive data of sales, profit, discount, profit ratio, customer names, order IDs and geographical information of SuperStore. We are going to analyse the dataset and provide a full picture of its business performance and problems with opportunities for business growth.

#### *1.2. Target Audience (Imaginative Personas)* 
Vice President of Sales: 
   * Needs an immediate actionable plan to start improving the loss situation
   * Is super busy with daily operation, so the analysis should be comprehensive with a meaningful story and easy to follow.
   * Has limited face time with executives, needs a concise analysis for data-driven decision making to sell to the executive team
   * Is a good sales person but not great with charting and numbers.
     
The Vice President of Sales is aware of the company discount policy is being abused but she does not know its exact magnitude. She is interested in learning how discounted sales is affecting the company sales and profitability so she can suggest a resonable adjustment to the discount scheme in the meeting with the exective team.

### 2. Analysis Direction
There is a signigicant loss for those discounted orders. While discounted orders generate $1,088,020 in sales (vs. $1,209,334 in sales by non-discounted orders), they generate a sub-zero profit of -$34,599.

We will look into what causes that loss by region, customer segment, and sub-categories, ect. and more specifically how to tackle this situation for business improvement. We are going to use Tableau, a powerful visualization tool that can convert a seemingly endless datasets into more visual and intuitive graphs, to analyze the dataset and demonstrate the whole business story for an actionable business plan.

*Disclaimer: Because this analysis is focused on an immediate actionable plan, this should be considered to be a part of the whole business plan. We are only going to discuss the areas where attention is needed first and foremost in this analysis.*

### 3. Analysis
Before we jump into the analysis, I would like to firstly make sure we are on the same page of those matrixes used in the analysis.

**Sales($)**: **Sum** of sales <br>
**Profit($)**: **Sum** of profit <br>
**Discount(%)**: **Average** of discount percent <br>
**Profit Ratio(%)**: **Average** of Profit Ratio <br>
**Discounted Orders**: All orders that get a discount of more than zero percent <br>
**Non-Discounted Orders**: All orders that get a discount of zero percent

The current situation: Discounted orders generate $1,088,020 in sales, equal 47% of the bussines revenue but generate a profit loss of -8.23%. And the average discount ratio for all the discounted orders is 30.04% (Figure 1).

*Figure 1*

<img src="/assets/images/P8_1.png">

When we look at Sales, Profit, Discount and Profit Ratio by Region, the picture becomes clearer that big loss is happening mainly to the Discounted Orders in the Central (profit ratio of -35.10%). Discounted Orders in the East and South are not making any profit. Only Discounted Orders in the West is making a modest profit ratio of 8.57% (Figure 2).

*Figure 2*

<img src="/assets/images/P8_2.png">

Let's look at the business performance by Customer Segment per region. Each circle demonstrates each customer. The color scheme depicts the profit ratio range from -100% to 100%. The darker the color is, the more loss the company is taking for a customer. It is worth noticing that customers with higher discount generate more negative profit ratio, mostly across all customer segments. However, most loss is seen in the Central (Figure 3).

*Figure 3*

<img src="/assets/images/P8_3.png">

<img src="/assets/images/P8_3b.png">


The "Performance by City" map below shows us more details about sales and profit ratio at city level where the size of the circle demonstrate the sales of a city while the color of the circle depicts the profit ratio of that city (red/orange means big negative profit ratio while dark blue means good positive profit ratio). We can see that big orange circles are found in the Central and the West of the country. These are the cities with big sales and big negative profit ratio (Figure 4). So what are those cities?

*Figure 4* 

<img src="/assets/images/P8_4b.png">

Figure 5 is a list of the underperforming cities in terms of Profit Ratio. The cities in Illinois and Texas are the most underperforming cities. The cities in Ohio and Pensylvania, although not as serverely, they should be paid attention to as well.

*Figure 5* 

<img src="/assets/images/P8_5.png">

So what are the specific sub-categories that the business is losing money in the Central and the East? While profitable sub-categories are mostly performing the the same in both regions. Binders and Appliances are especially taking big loss in the Central (Figure 6).

Philadelphia city should also be paid attention to with its highest sales number of $109,085 among all the cities but has a negative profit ratio of -7.99%. This city alone contributes 4.74% of the total business sales. <br>

*Figure 6* 

<img src="/assets/images/P8_6.png">

Figure 7 consists of customers in the Central and East only. Each bubble stands for a customer, the color of the bubble tells us about the the profit ratio the company has with that customer and the size indicates the discount percent. For most big bubbles (i.e heavily discounted customers), the color is orange to brown-ish, meaning the company are losing money (negative profit ratio) with these customers. 

*Note that the tooltip (the rectangle text box) is demonstrating information of a bubble (i.e a customer) on the chart*

*Figure 7* 

<img src="/assets/images/P8_7.png">

While Figure 7 is useful in confirming the hypothesis that the larger discount the company offers, the more loss the company gets. However, it does not show us how many customers in each discount tier.

Figure 8 does a good job in demonstrating how many customers we have in each discount tier. Also, it helps discover another interesting fact that those customers that receive lower discount rate actually make bigger sales then the heavily discounted ones. This leads to necessity of large discount, because it does not incentivize larger sales. 

Also, the profit ratio starts to turn negative at around 25% discount (notice the white-ish bubbles depicting 0% profit ratio). This 25% discount level should be used as a threshold for building the company's sales strategy.

*Note that the tooltip (the rectangle text box) is demonstrating information of a bubble (i.e a customer) on the chart*

*Figure 8* 

<img src="/assets/images/P8_8.png">

### 4. Summary of Findings

It is clearly evident that discount scheme of SuperStore is abused to the point it is hurting the business profit. 

While the discounted orders mounted up to 47% of the total business revenue, it results in -$34,599 loss in profit, equal -8.23% profit ratio. 

Performance is best in the West while loss in profit is mained observed in the Central. The East and South can hardly break even. 

Loss is seen across all customer segments for heavily discounted orders, most are in the Central. 

Illinois and Texas are the two states to start with adjusting the discount scheme. Chicago, Aurora city in Illinois and Houston, San Antonio and Dallas city in Texas are seriously underperforming with great sales and highly negative profit ratio. 

Philadelphia city should also be paid attention to because it generates the highest sales ($109,085) but has a negative profit ratio of -7.99%. This city alone contributes 4.74% of the total business sales, if we can convert its profit ratio to a high positive percent then it will significantly contributes to the overall profit of the business. 

In terms of performance by sub-category, profit ratio of appliances, binders and furnishings needs to be immediately improved in order to gain postive profit for the Central region. In the East, it is mostly Tables and Machines that needs to be fixed in terms of the profit ratio they generate. 

Following the bubble chart, we could identify those individual customers with large discounts and large negative profit ratio. The modification should start from those. 

We have found that the company starts to have a mixture of positive and negative profit ratios at around 25% discount. SuperStore should use this 25% discount level as a threshold and start to design a sales strategy around that level.




