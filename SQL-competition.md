[//]: # (Pulkit)
1) Find the Indian Staff from whom customer rented film language id 1.
```postgresql
   Select count(*)
   from country c
       inner join city c2 on c.country_id = c2.country_id
       inner join public.address a on c2.city_id = a.city_id
       inner join staff s on a.address_id = s.address_id
       inner join rental r on s.staff_id = r.staff_id
       inner join inventory i on r.inventory_id = i.inventory_id
       inner join film f on i.film_id = f.film_id
   where c.country = 'India'
   and f.language_id = 1;
```
2) Find the Location of store where film was rented whose release year is 2006.
```postgresql
Select a.district, film.film_id
from address a
         inner join store on a.address_id = store.address_id
         inner join staff on store.address_id = staff.address_id
         inner join rental on staff.staff_id = rental.staff_id
         inner join inventory on rental.inventory_id = inventory.inventory_id
         inner join film on film.film_id = inventory.film_id
where film.release_year = 2006
group by a.district, film.film_id;
```
[//]: # (Shreyash)
3) select 25 the actors firstname and lastname in one column named as name and their film names who have released in recent year 2022 order by name
```postgresql
Select film.title, concat(actor.first_name, actor.last_name) as name
from actor
         inner join film_actor on actor.actor_id = film_actor.actor_id
         inner join film on film_actor.film_id = film.film_id
where release_year = 2006
order by name
LIMIT 25;
```
4) select the staffs firstname and lastname in one column and their whole payment
```postgresql
select concat(s.first_name, ' ', s.last_name) as staff_name,
       sum(p.amount) as payment
from staff s
         inner join payment p
                    on s.staff_id = p.staff_id
group by staff_name;
```

[//]: # (Nandini)
5) list out all films with highest rental rate  and classify the films labelled as "inappropriate for children under 13" with ratings PG-13 , "General" with ratings as G and " Restricted for Age below 18" with ratings as R
```postgresql
select film.title,
       film.rental_rate,
       case
           when film.rating = 'PG-13' then 'Restricted for Age below 18'
           when film.rating = 'G' then 'General'
           when film.rating = 'R' then 'Restricted for Age below 18'
           else 'Unknown'
           end as classification
from film
WHERE film.rental_rate = (SELECT MAX(rental_rate) FROM film)
order by film.title;
```
6) list the top 3 actors with maximum sci-fi movies and highest payment amount
```postgresql
Select actor.first_name, actor.last_name, category.name, count(*) as count
from actor
         inner join film_actor on actor.actor_id = film_actor.actor_id
         inner join film on film_actor.film_id = film.film_id
         inner join film_category on film.film_id = film_category.film_id
         inner join category on film_category.category_id = category.category_id
where category.name = 'Sci-Fi'
group by first_name, last_name, category.name, film.rental_rate
order by count(*), film.rental_rate desc
limit 3;
```

[//]: # (Harshit)
7) Determine which customer watch how many movies of particular actor.
```postgresql
select concat(customer.first_name, customer.last_name) as customer_name,
       concat(actor.first_name, actor.last_name)       as actor_name,
       count(rental_id)                                as movies
from actor
         inner join film_actor on actor.actor_id = film_actor.actor_id
         inner join film on film_actor.film_id = film.film_id
         inner join inventory on film.film_id = inventory.film_id
         inner join rental on inventory.inventory_id = rental.inventory_id
         inner join customer on rental.customer_id = customer.customer_id
group by actor_name, customer_name, rental.customer_id, actor.actor_id
order by movies desc;

```
8) List out top 10 most revenue generated district to release animated film with average price running on that district
```postgresql
Select address.district, sum(amount) as total_amount
from address
         inner join customer on address.address_id = customer.address_id
         inner join payment on customer.customer_id = payment.customer_id
         inner join rental on customer.customer_id = rental.customer_id
         inner join inventory on rental.inventory_id = inventory.inventory_id
         inner join film on inventory.film_id = film.film_id
group by address.district, payment.customer_id
order by total_amount desc limit 10;

```

[//]: # (Sadid)
9) Find all actors who acted in more than one language. Output should contain actor_name, language. Sorted by languages, actor_name.
```postgresql
SELECT concat(actor.first_name, actor.last_name) as actor_name, language.name
from actor
         inner join film_actor on actor.actor_id = film_actor.actor_id
         inner join film on film_actor.film_id = film.film_id
         inner join language on film.language_id = language.language_id
group by actor_name, language.name
having count(language) > 1
order by actor_name;
```

[//]: # (Vaishnav)
10)  What are the top 10 films that have been rented by customers in the United States, and how many times have they been rented?
```postgresql
Select film.title, count(country.country) as rental_films
from film
         inner join inventory on film.film_id = inventory.film_id
         inner join rental on inventory.inventory_id = rental.inventory_id
         inner join customer on rental.customer_id = customer.customer_id
         inner join address on customer.address_id = address.address_id
         inner join city on address.city_id = city.city_id
         inner join country on city.country_id = country.country_id
where country = 'United States'
group by film.title
order by rental_films desc
limit 10;
```
11) What is the average rental duration for the top 5 films that have been rented by customers in each country?

```postgresql
Select c.country as top_country, film.title, avg(film.length) as duration
from film
         inner join inventory on film.film_id = inventory.film_id
         inner join rental on inventory.inventory_id = rental.inventory_id
         inner join customer on rental.customer_id = customer.customer_id
         inner join address on customer.address_id = address.address_id
         inner join city on address.city_id = city.city_id
         inner join country c on city.country_id = c.country_id
group by film.title, top_country
order by duration desc
limit 5;
```

[//]: # (Tejas)
12) Query to Find Actors Who Have Collaborated the Most.
```postgresql
SELECT a1.actor_id AS actor1_id,a1.first_name AS actor1_first_name,
       a2.actor_id AS actor2_id,
       a2.first_name AS actor2_first_name,
       COUNT(DISTINCT fa1.film_id) AS shared_film_count
FROM actor a1
         JOIN film_actor fa1 ON a1.actor_id = fa1.actor_id
         JOIN film_actor fa2 ON fa1.film_id = fa2.film_id AND fa1.actor_id != fa2.actor_id
         JOIN actor a2 ON fa2.actor_id = a2.actor_id
GROUP BY a1.actor_id, a1.first_name, a2.actor_id, a2.first_name
ORDER BY shared_film_count DESC
LIMIT 1;
```
13) Query to Find Countries with the Longest Average Address Street Length.
```postgresql
Select country.country,
       avg(length(address.address)) as street_length
from country
         inner join city on country.country_id = city.country_id
         inner join address on city.city_id = address.city_id
group by country.country
order by street_length desc;
```

[//]: # (Sadid-2)
14) Find the category which was rented the most for each month in year 2005, if there is a tie find all categories. Sorted by months and category names. Output should contain month, category, count. You can ignore the month if there is no data present for it.
```postgresql
select c.name , count(*) from film f
                                  inner join inventory i
                                             on f.film_id = i.film_id
                                  inner join rental
                                             on i.inventory_id = rental.inventory_id
                                  inner join film_category fc
                                             on f.film_id = fc.film_id
                                  inner join category c
                                             on fc.category_id = c.category_id
group by c.name
order by count(*) desc;
```