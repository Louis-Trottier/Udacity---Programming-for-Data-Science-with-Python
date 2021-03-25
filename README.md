# Investigate-a-Relational-Database

# Write SQL code to run SQL queries to explore a database related to movie rentals

# Query 1
# Which film category is most popular among family friendly movies?
SELECT film.title AS film_title,

	category.name AS category_name,
	
	COUNT (rental.rental_id) AS rental_count
	
FROM category

JOIN film_category

ON film_category.category_id = category.category_id

JOIN film

ON film.film_id = film_category.film_id

JOIN inventory

ON film.film_id = inventory.film_id

JOIN rental

ON inventory.inventory_id = rental.inventory_id

WHERE category.name = 'Animation' or category.name = 'Children' or category.name = 'Classics' or category.name = 'Comedy' or category.name = 'Family' or category.name = 'Music'

GROUP BY 1,2

ORDER BY 2,3 DESC

# Query 2
# Which month(s) is most popular for family-friendly film category movies to be rented?
SELECT name AS category,

	rental_month,
	
	count(*) AS rental_count, 
	
	LEAD (count(*)) OVER (PARTITION BY name ORDER BY count(*)) AS lead,
	
	LEAD (count(*)) OVER (PARTITION BY name ORDER BY count(*)) - count(*) AS difference
	
FROM (SELECT category.name AS name,

	DATE_PART ('month', rental.rental_date) AS rental_month
	
	FROM category
	
	JOIN film_category
	
	ON category.category_id = film_category.category_id
	
	JOIN film
	
	ON film_category.film_id = film.film_id
	
	JOIN inventory
	
	ON film.film_id = inventory.film_id
	
	JOIN rental
	
	ON inventory.inventory_id = rental.inventory_id
	
	WHERE category.name = 'Animation' or category.name = 'Children' or category.name = 'Classics' or category.name = 'Comedy' or category.name = 'Family' or category.name = 'Music') sub
	
GROUP BY 1,2

ORDER BY 1,2

# Query 3
# Which store had the most rentals in August of 2005?
SELECT DATE_PART ('month', rental.rental_date) AS Rental_month,

	DATE_PART ('year', rental.rental_date) AS Rental_year,
	
	customer.store_id AS Store_ID,
	
	COUNT (rental.rental_id) AS Count_rentals
	
FROM rental

JOIN customer

ON rental.customer_id = customer.customer_id

GROUP BY 1,2,3

ORDER BY 4 DESC

# Query 4
# Which of the top 10 customers rented the most movies in each month of 2007?
WITH top10 AS 

(SELECT DISTINCT(customer.customer_id) AS customer_id,

	customer.first_name || ' ' || customer.last_name AS name,
	
	SUM(payment.amount) AS sum
	
FROM customer 

JOIN payment 

ON customer.customer_id = payment.customer_id

GROUP BY 1,2

ORDER BY 3 DESC

LIMIT 10)

SELECT DATE_TRUNC('month', payment.payment_date) AS pay_mon,

	top10.name AS fullname, 
	
	COUNT(payment.payment_id) as countpermonth, 
	
	SUM(payment.amount) as totalpayamount
FROM top10

JOIN payment 

ON payment.customer_id = top10.customer_id

GROUP BY 1,2

ORDER BY 2;
 
