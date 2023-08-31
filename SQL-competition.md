[//]: # (Pulkit)
1) Find the Indian Staff whose goods are used in making Hindi films with rating 7.5 or above.
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
2) Find the Location of stores whose goods are used for making films whose release year is 2005.
```postgresql
Select a.district, film.film_id
from address a
         inner join staff on a.address_id = staff.address_id
         inner join rental on staff.staff_id = rental.staff_id
         inner join inventory on rental.inventory_id = inventory.inventory_id
         inner join film on film.film_id = inventory.film_id
where film.release_year = 2005
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
LIMIT 25;
```
4) select the staffs firstname and lastname in one column and their whole payment
```postgresql
Select concat(actor.first_name, actor.last_name) as name, payment.amount
from actor
         inner join film_actor on actor.actor_id = film_actor.actor_id
         inner join film on film_actor.film_id = film.film_id
         inner join inventory on film.film_id = inventory.film_id
         inner join rental on inventory.inventory_id = rental.inventory_id
         inner join payment on rental.rental_id = payment.rental_id
group by name, payment_id;
```
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
order by customer_name, actor_name;

```
8) List out top 10 most revenue generated district to release animated film with average price running on that district
```postgresql
Select address.district,sum(amount) as total_amount from address
inner join customer on address.address_id = customer.address_id
inner join payment on customer.customer_id = payment.customer_id
inner join rental on customer.customer_id = rental.customer_id
inner join inventory on rental.inventory_id = inventory.inventory_id
inner join film on inventory.film_id = film.film_id
group by address.district,payment.customer_id
order by sum(amount);

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

