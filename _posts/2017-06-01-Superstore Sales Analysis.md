---
title:  "Superstore Sales Analysis"
mathjax: true
layout: post
categories: media
---

<img style="float:left" src="/assets/images/Image8_small.png">

Superstore dataset is includes sales information of a small retail business named SuperStore. We will analyze the dataset and use Tableau to develop a story line for visualization and analysis.



### Table of Content
   * Introduction
   * Analysis Outline
   * Analysis
   * Summary of Findings

### 1. Introduction
#### *1.1. The Dataset*
SuperStore dataset is a demonstrative dataset of a small retail business located in the United States which sells Furniture, Office Supplies and Technology products to their customers consisting of the mass Consumers, Corporates and Home Offices.

This dataset is from the series of “Data Visualization with Tableau” I took on Coursera. It can be found [here]().

The dataset contains comprehensive data of sales, profit, discount, profit ratio, customer names, order IDs and geographical information of SuperStore. We are going to analyse the dataset and provide a full picture of its business performance and problems with opportunities for business growth.

#### *1.2. Target Audience (Immaginative Personas)* 
Vice President of Sales: 
   * Needs an analysis in order to turn it into a sales strategy she can sell to the executive team
   * Has limited face time with executives, needs a concise yet comprehensive analysis for data-driven decision making
   * Is a good sales person but not great with charting and numbers.
   * Is busy dealing with reports and KPIs, so the analysis should help tell a meaningful story.
     
The Vice President of Sales is aware of the company discount policy is being abused but she does not know its exact magnitude. She is interested in learning how discounted sales is affecting the company sales and profitability so she can suggest a resonable adjustment to the discount scheme in the meeting with the exective team.

### 2. Analysis Outline
There is a signigicant loss for those discounted orders. While discounted orders generate $1,088,020 in sales (vs. $1,209,334 in sales by non-discounted orders), they generate a sub-zero profit of -$34,599.

We will look into what causes that loss by region, customer segment, and sub-categories, ect. and more specifically how to tackle this situation for business improvement. We are going to use Tableau, a powerful visualization tool that can convert a seemingly endless datasets into more visual and intuitive graphs, to analyze the dataset and demonstrate the whole business story for an actionable business plan.

### 3. Analysis
Before we jump into the analysis, I would like to firstly make sure we are on the same page of those matrixes used in the analysis.

**Sales($)**: **Sum** of sales
**Profit($)**: **Sum** of profit
**Discount(%)**: **Average** of discount percent
**Profit Ratio(%)**: **Average** of Profit Ratio
**Discounted Orders**: All orders that get a discount of more than zero percent
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

<img style="float:left" src="/assets/images/P8_3b.png">

The "Performance by City" map below shows us more details about sales and profit ratio at city level where the size of the circle demonstrate the sales of a city while the color of the circle depicts the profit ratio of that city (red/orange means big negative profit ratio while dark blue means good positive profit ratio). We can see that big orange circles are found in the Central and the West of the country. These are the cities with big sales and big negative profit ratio (Figure 4). So what are those cities?

*Figure 4* 

<img src="/assets/images/P8_4.png">

Figure 5 is a list of the underperforming cities in terms of Profit Ratio. The cities in Illinois and Texas are the most underperforming cities. The cities in Ohio and Pensylvania, although not as serverely, they should be paid attention to as well.

*Figure 5* 
<img src="/assets/images/P8_5.png">

So what are the specific sub-categories that the business is losing money in the Central and the East? While profitable sub-categories are mostly performing the the same in both regions. Binders and Appliances are especially taking big loss in the Central (Figure 6).

*Figure 6* 

<img src="/assets/images/P8_6.png">

Figure 7 consists of customers in the Central and East only. Each bubble stands for a customer, the color of the bubble tells us about the the profit ratio the company has with that customer and the size indicates the discount percent. For most big bubbles (i.e heavily discounted customers), the color is orange to brown-ish, meaning the company are losing money (negative profit ratio) with these customers. 

*Figure 7* 

<img src="/assets/images/P8_7.png">

While Figure 7 is useful in confirming the hypothesis that the larger discount the company offers, the more loss the company gets. However, it does not show us how many customers in each discount tier.

Figure 8 does a good job in demonstrating how many customers we have in each discount tier. Also, it helps discover another interesting fact that those customers that receive lower discount rate actually make bigger sales then the heavily discounted ones. This leads to necessity of large discount, because it does not incentivize larger sales. 

Also, the profit ratio start to turn negative at around 25% discount (notice the white-ish bubbles depicting 0% profit ratio). The company should use 25% discount level as it threshold and start to design a strategy around that level.


*Figure 8* 

<img src="/assets/images/P8_8.png">
