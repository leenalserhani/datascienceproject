# datascienceproject
--Set 1 :

--First query :

SELECT f.title AS film_tile,
       c.name AS category_name,
       count(r.rental_id)
FROM film f
INNER JOIN film_category fc ON f.film_id= fc.film_id
INNER JOIN category c ON fc.category_id= c.category_id
INNER JOIN inventory i ON i.film_id = r.inventory_id
GROUP BY f.title,
         c.name;

--Second query :

SELECT f.title,
       c.name,
       f.rental_duration,
       ntile(4) OVER (
                      ORDER BY f.rental_duration) AS standar_quartile
FROM film f
INNER JOIN film_category fc ON fc.film_id=f.film_id
JOIN category c ON c.category_id=fc.category_id;


--Third query :
SELECT name,
       standar_quartile,
       COUNT (standar_quartile)
FROM
  (SELECT c.name AS name ,
          ntile(4) OVER (
                         ORDER BY f.rental_duration) AS standar_quartile
   FROM film f
   INNER JOIN film_category fc ON f.film_id = fc.film_id
   INNER JOIN category c ON fc.category_id= c.category_id)table1
GROUP BY name,
         standar_quartile
ORDER BY name,
         standar_quartile ;

--Set 2 :

--First query:

SELECT Date_part('month', r.rental_date)AS rental_month,
       Date_part('year', r.rental_date)AS rental_year,
       s.store_id,
       COUNT(r.rental_id) AS count_rentals
FROM rental r
INNER JOIN staff st USING (staff_id)
INNER JOIN store s USING (store_id)
GROUP BY rental_month,
         rental_year,
         store_id
ORDER BY count_rentals;

--Second query:
SELECT date_trunc('month', p.payment_date) AS pay_month ,
       c.first_name || ' ' || c.last_name AS fullname,
       count(p.payment_id)AS pay_countpermon,
       sum(p.amount) AS pay_amount
FROM
  (SELECT p.customer_id AS cid,
          sum(p.amount) AS pp
   FROM payment p
   WHERE extract(YEAR
                 FROM p.payment_date) = 2007
   GROUP BY p.customer_id
   ORDER BY pp DESC
   LIMIT 10) yearamount
INNER JOIN customer c ON yearamount.cid=c.customer_id
INNER JOIN payment p ON yearamount.cid=p.customer_id
GROUP BY pay_month,
         c.customer_id
ORDER BY fullname,
         pay_month
