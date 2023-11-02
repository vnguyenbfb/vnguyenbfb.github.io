---
title:  "SQL Project - Movie DVD Rental Database"
mathjax: true
layout: post
categories: media
---

<img style="float:left" src="/assets/images/Image1_small.png">

### Overview
Executive summary
Schema Diagram
Business Objectives
Analysis: Source Code, Visualization and Findings
Summary of main findings & Recommendations
Reference


### Executive summary
The Sakila Database is about a movie DVDs rental company. The database consists of 15 tables:
   * actor – stores actors data including first name and last name.
   * film – stores film data such as title, release year, length, rating, etc.
   * film_actor – stores the relationships between films and actors.
   * category – stores film’s categories data.
   * film_category- stores the relationships between films and categories.
   * store – contains the store data including manager staff and address.
   * inventory – stores inventory data.
   * rental – stores rental data.
   * payment – stores customer’s payments.
   * staff – stores staff data.
   * customer – stores customer data.
   * address – stores address data for staff and customers
   * city – stores city names.
   * country – stores country names.<br>

The movie DVDs rental database can be downloaded [here](https://www.postgresqltutorial.com/wp-content/uploads/2019/05/dvdrental.zip). The database file is in zipformat ( dvdrental.zip) so you need to extract it to  dvdrental.tar before loading the sample database into the PostgreSQL database server. <br>
Printable ER diagram can be found [here](https://www.postgresqltutorial.com/wp-content/uploads/2018/03/printable-postgresql-sample-database-diagram.pdf). <br>
We are going to use SQL to query the database to gain understanding of the company’s business. The schema for the DVD Rental database is provided as below.

### Schema Diagram
<image src="/assets/images/dvd-rental-erd-2.png"/>

### Business Objectives
1. Compare numbers of rental orders by store
2. Numbers of movies in each rental duration category
3. Payments by the top 10 paying customers on a monthly basis during 2007
4. Difference across the monthly payments during 2007 among the top 10 paying customers
5. Count the times each film category has been rented and their average rental duration

### Analysis: Source Code, Visualization and Findings
#### 1. Compare numbers of rental orders by store
```
SELECT EXTRACT(month FROM rental.rental_date) AS rental_month, EXTRACT(year FROM rental.rental_date) AS rental_year,
    store.store_id AS storeid, COUNT(store.store_id) AS count_rentals
FROM rental
JOIN staff ON rental.staff_id = staff.staff_id
JOIN store ON staff.store_id = store.store_id
GROUP BY rental_month, rental_year, storeid
ORDER BY count_rentals DESC;
```
<img style="float:left" src="/assets/images/P1_1.png">
Over the recorded months, the 2 stores have quite similar numbers of rental orders. The chart shows highest numbers in July 2005 and lowest numbers in February 2006. 

#### 2. Numbers of movies in each rental duration category
```
WITH table1 AS
(   SELECT film.title AS title, category.name AS name, ROUND(AVG(rental_duration), 0) AS avg_rental_duration, 
    NTILE(4) OVER (ORDER BY AVG(rental_duration)) AS quartile                         
    FROM category
    JOIN film_category ON category.category_id = film_category.category_id              
    JOIN film ON film.film_id = film_category.film_id
    WHERE name IN ('Animation', 'Children', 'Classics', 'Comedy', 'Family', 'Music')
    GROUP BY title, name)

SELECT name, quartile, COUNT(*)
FROM table1
GROUP BY name, quartile
ORDER BY name, quartile;   
```
<img style="float:left" src="/assets/images/P1_2.png">
Most film categories have even distribution among the quartiles, except for Classics and Music. Classics has large difference between quartiles 1, 4 and quartiles 2, 3 while Music has large difference between quartile 1 and 3.

#### 3.	Payments by the top 10 paying customers on a monthly basis during 2007
```
WITH table1 AS
(   SELECT customer_id, SUM(amount) total_amount
    FROM payment
    GROUP BY customer_id
    ORDER BY total_amount DESC
    LIMIT 10)

SELECT DATE_TRUNC('month', payment.payment_date) AS pay_mon, CONCAT(customer.first_name, ' ',customer.last_name) AS fullname, 
        COUNT(payment_id) AS paycount_permonth, SUM(payment.amount) AS pay_amount
FROM payment 
JOIN customer ON payment.customer_id = customer.customer_id
WHERE payment.customer_id IN (SELECT customer_id FROM table1)
GROUP BY pay_mon, fullname
ORDER BY fullname; 
```
<img style="float:left" src="/assets/images/P1_3.png">
The top 10 paying customers made the most payment amounts in April 2007 and least payment amounts in May 2007. There is an uptrend in terms of payment amounts from February to April 2007 in 8 out of 10 top payment customers.

#### 4.	Difference across the monthly payments during 2007 among the top 10 paying customers
```
WITH table1 AS
(   SELECT customer_id, SUM(amount) total_amount
    FROM payment
    GROUP BY customer_id
    ORDER BY total_amount DESC
    LIMIT 10),

table2 AS
(   SELECT DATE_TRUNC('month', payment.payment_date) AS pay_mon, CONCAT(customer.first_name, ' ',customer.last_name) AS fullname, SUM(payment.amount) AS pay_amount
    FROM payment 
    JOIN customer ON payment.customer_id = customer.customer_id
    WHERE payment.customer_id IN (SELECT customer_id FROM table1)
    GROUP BY pay_mon, fullname
    ORDER BY fullname),

table3 AS
(   SELECT pay_mon, fullname, LEAD(fullname) OVER () AS next_fullname, pay_amount, LEAD(pay_amount) OVER () AS next_month_amount
    FROM table2)

SELECT pay_mon, fullname, pay_amount, next_month_amount, (next_month_amount - pay_amount) AS diff_fr_last_month
FROM table3
WHERE fullname = next_fullname
ORDER BY diff_fr_last_month DESC;
```
<img style="float:left" src="/assets/images/P1_4.png">
Eleanor H. ($64.9) had the most increase in payment. Closely following are Curtis I. ($63.9) and Rhonda K. ($54.9). All of those records are observed in March 2007.

#### 5.	Count the times each film category has been rented and their average rental duration
```
SELECT category.name AS category_name, COUNT(rental.rental_id) AS rental_count, 	AVG(rental_duration) AS avg_rental_duration
FROM category
JOIN film_category ON category.category_id = film_category.category_id
JOIN film ON film.film_id = film_category.film_id
JOIN inventory ON inventory.film_id = film.film_id
JOIN rental ON rental.inventory_id = inventory.inventory_id
WHERE category.name IN ('Animation', 'Children', 'Classics', 'Comedy', 'Family', 'Music')
GROUP BY category_name
ORDER BY rental_count;
```
<img style="float:left" src="/assets/images/P1_5.png">
Animation is the most frequently rented category (1166 times). Family came as a close second (1096 times) and Children came third (945 times). Music is the least rented category (830 times).
It is quite interesting that the most rented category (Animation) has the shortest average rental duration (4.89 days) while the least rented category (Music) has the longest average rental duration (5.24 days).

### Summary of main findings and Recommendations

   * There is not much difference in terms of number of rental orders between the two stores. The most counts are observed in July 2005 and the least in February 2006. The dataset only provides monthly data from May to August 2005 and February 2006. If there is more continuous monthly data, we could look at trends in terms of rental orders on a monthly basis.
   * Most film categories show even distribution among the quartiles. However, Classics and Music observe the largest gaps among quartiles - quartiles 1, 4 versus quartiles 2, 3 for Classics and quartile 1 versus quartile 3 for Music.
   * The top 10 paying customers made the most payment amounts in April 2007 and least in May 2007. Data of May 2007 is significanly little compared to other months. If we remove May 2007 data from the picture, there is an up trend in the payment amounts by 8 out of 10 top paying customers between February and April 2007. More investigation should be launched to find out whether May 2007 data is complete to determine the real trend behind these payment amounts. The company can conduct customer satisfaction survey to understand the unmet needs by Ana B. and Mike W. to explore more sales opportunities.
   * Eleanor H. ($64.9) had the most increase in payment and that happened in March 2007. Closely following are Curtis I. ($63.9) and Rhonda K. ($54.9). All of those records are observed in March 2007. Besides those best increases, we also need to look into those customers who are paying less such as Mike W. (-$80.83), Marcia E. (-$72.8) and Ana B. (-$69.9). These are noticed in May 2007.
   * Animation is the most frequently rented category (1166 times). Family came as a close second (1096 times) and Children came third (945 times). Music is the least rented category (830 times).
   * It is worth mentioning that the most rented category (Animation) has the shortest average rental duration (4.89 days) while the least rented category (Music) has the longest average rental duration (5.24 days). More research should be done to understand the nature of each film category to understand what drives the rental duration. For example, average length of each film category, customers’ behavoir and profiles, ect…
### Reference:
https://www.postgresqltutorial.com/postgresql-getting-started/postgresql-sample-database/

