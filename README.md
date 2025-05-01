```
set search_path to luxteaching;
```
# Basic Queries
1. All customers with their full name and city
```
select concat(first_name,' ',last_name) as Names, city 
from customers;
```
names      |city   |
-----------|-------|
John Doe   |Nairobi|
Jane Smith |Mombasa|
Paul Otieno|Kisumu |
Mary Okello|Nairobi|

2. All books priced above 2000
```
select * from books
where price > 2000;
```
book_id|title                     |author      |price  |published_date|
-------|--------------------------|------------|-------|--------------|
2   |Data Analytics Basics     |Susan Njeri |2200.88|    2023-03-05|
3      |Data Analytics for Dummies|Richard Oino|2600.89|    2023-04-23|


--3. Customers living in Nairobi
```
select *
from customers
where city ='Nairobi';
```
customer_id|first_name|last_name|email             |phone_number |city   |
-----------|----------|---------|------------------|-------------|-------|
 1  |John      |Doe      |johndoe@gmail.com |+254639738701|Nairobi|
4   |Mary      |Okello   |maryjane@gmail.com|+254652396453|Nairobi|

--4. All book titles published in 2023
```
select * from books
where published_date between '2023-01-01' and '2023-12-31';
```
book_id|title                     |author      |price  |published_date|
-------|--------------------------|------------|-------|--------------|
 1|Understanding SQL         |David Kimani|1500.35|    2023-01-15|
 2|Data Analytics Basics     |Susan Njeri |2200.88|    2023-03-05|
 3|Data Analytics for Dummies|Richard Oino|2600.89|    2023-04-23|

--5. All orders placed after March 1st 2025
```
select * from orders
where order_date > '2025-03-01';
```
order_id|customer_id|book_id|order_date|quantities|
--------|-----------|-------|----------|----------|
1|          1|      3|2025-04-01|         2|
3|          3|      2|2025-04-03|         3|
4|          4|      4|2025-04-04|         2|
5|          1|      2|2025-04-05|         1|
2|          2|      1|2025-04-02|         1|
#   Filtering and Sorting

--6. All books ordered, sorted by price(desceding)
```
select orders.order_id, books.title, books.price
from books
inner join orders on books.book_id = orders.book_id
order by price desc;
```

order_id|customer_id|book_id|order_date|quantities|
--------|-----------|-------|----------|----------|
   1|          1|      3|2025-04-01|         2|
   3|          3|      2|2025-04-03|         3|
   4|          4|      4|2025-04-04|         2|
   5|          1|      2|2025-04-05|         1|
   2|          2|      1|2025-04-02|         1|

--7. Show all customers whose names start with 'J'. 
```
select * from customers
where first_name like 'J%';
```
customer_id|first_name|last_name|email              |phone_number |city   |
-----------|----------|---------|-------------------|-------------|-------|
 1|John      |Doe      |johndoe@gmail.com  |+254639738701|Nairobi|
  2|Jane      |Smith    |johnsmith@gmail.com|+254639797879|Mombasa|

--8. List books with prices between 1500 and 3000.
```
select * from books 
where price between 1500 and 3000;
```
book_id|title                     |author        |price  |published_date|
-------|--------------------------|--------------|-------|--------------|
 1|Understanding SQL         |David Kimani  |1500.35|    2023-01-15|
 2|Data Analytics Basics     |Susan Njeri   |2200.88|    2023-03-05|
 3|Data Analytics for Dummies|Richard Oino  |2600.89|    2023-04-23|
 4|Python for ML             |Favour Chumbah|1900.99|    2024-02-14|

#			Aggregate Functions and Grouping 

--9. Count the number of customers in each city. 
```
select city, count(*) as city_customers
from customers
group by city;
```

city   |city_customers|
-------|--------------|
Kisumu |             1|
Mombasa|             1|
Nairobi|             2|

--10. Show the total number of orders per customer. 
```
select customer_id, count(customer_id) as Total_No_of_orders
from orders
group by customer_id;
```

customer_id|total_no_of_orders|
-----------|------------------|
  3|                 1|
  4|                 1|
  2|                 1|
  1|                 2|

--11. Find the average price of books in the store. 
```
select avg(price) as Average_price
from books;
```
average_price        |
---------------------|
2050.7775000000000000|

--12. List the book title and total quantity ordered for each book.
```
select books.title, sum(orders.quantities) as quantity_ordered
from books
left join orders on books.book_id = orders.book_id
group by books.title;
```
title                     |quantity_ordered|
--------------------------|----------------|
Data Analytics for Dummies|               2|
Python for ML             |               2|
Data Analytics Basics     |               4|
Understanding SQL         |               1|

#			Subqueries 
--13. Show customers who have placed more orders than customer with ID = 1. 
```
select customer_id, count(order_id) as total_order
from orders
group by customer_id 
having count(order_id) >
   (select 
	count(order_id)
	from orders
	where customer_id = 1);
```
customer_id|total_order|
-----------|-----------|


--14. List books that are more expensive than the average book price. 
```
select title from books
where price > (select avg(price) from books);
```
title                     |price  |
--------------------------|-------|
Data Analytics Basics     |2200.88|
Data Analytics for Dummies|2600.89|

--15. Show each customer and the number of orders they placed using a subquery in SELECT.

```
select c.customer_id, c.first_name, (
        select count(*) 
        FROM orders o 
        WHERE o.customer_id = c.customer_id
    ) "total_orders"
from customers c;
```
customer_id|first_name|total_orders|
-----------|----------|------------|
  1|John      |           2|
  2|Jane      |           1|
  3|Paul      |           1|
  4|Mary      |           1|

#				JOINS 
--16. Show full name of each customer and the titles of books they ordered.
```
select concat(customers.first_name, ' ', customers.last_name) as full_names, books.title
from customers
left join orders on customers.customer_id = orders.customer_id
left join books on books.book_id = orders.book_id;
```
full_names |title                     |
-----------|--------------------------|
John Doe   |Data Analytics for Dummies|
Paul Otieno|Data Analytics Basics     |
Mary Okello|Python for ML             |
John Doe   |Data Analytics Basics     |
Jane Smith |Understanding SQL         |

--17. List all orders including book title, quantity, and total cost (price × quantity). 
```
select books.title, orders.quantities, books.price * orders.quantities as total_cost
from books
join orders on books.book_id = orders.book_id;
```
title                     |quantities|total_cost|
--------------------------|----------|----------|
Data Analytics for Dummies|         2|   5201.78|
Data Analytics Basics     |         3|   6602.64|
Python for ML             |         2|   3801.98|
Data Analytics Basics     |         1|   2200.88|
Understanding SQL         |         1|   1500.35|

--18. Show customers who haven't placed any orders (LEFT JOIN). 
```
select customers.first_name, customers.last_name, orders.order_id
from customers
left join orders on customers.customer_id = orders.customer_id
where order_id isnull;
```
first_name|last_name|order_id|
----------|---------|--------|

--19. List all books and the names of customers who ordered them, if any (LEFT JOIN).
```
select books.title, concat(customers.first_name, ' ', customers.last_name) as full_names
from orders 
left join customers on customers.customer_id = orders.customer_id
left join books on orders.book_id = books.book_id;
```
title                     |full_names |
--------------------------|-----------|
Data Analytics for Dummies|John Doe   |
Data Analytics Basics     |Paul Otieno|
Python for ML             |Mary Okello|
Data Analytics Basics     |John Doe   |
Understanding SQL         |Jane Smith |

--20. Show customers who live in the same city (SELF JOIN).
```
select a.first_name, a.last_name, b.city
from customers a
join customers b on a.city = b.city;
```
first_name|last_name|city   |
----------|---------|-------|
John      |Doe      |Nairobi|
John      |Doe      |Nairobi|
Jane      |Smith    |Mombasa|
Paul      |Otieno   |Kisumu |
Mary      |Okello   |Nairobi|
Mary      |Okello   |Nairobi|


#			Combined Logic 
--21. Show all customers who placed more than 2 orders for books priced over 2000. 
```
select c.customer_id, c.first_name, c.last_name,  count(*) "Order > 2"
from customers c
join orders o on c.customer_id = o.customer_id
join books b on o.book_id = b.book_id
where b.price > 2000
group by c.customer_id, c.first_name, c.last_name
having count(*) > 1;
```
customer_id|first_name|last_name|Order > 2|
-----------|----------|---------|---------|
  1|John      |Doe      |        2|

--22. List customers who ordered the same book more than once. 
```
select distinct books.title, customers.first_name, orders.customer_id
from customers
join orders on customers.customer_id = orders.customer_id
join books on orders.book_id = books.book_id
group by customers.first_name, orders.customer_id, books.title
having count(orders.book_id) > 1;
```
title|first_name|customer_id|
-----|----------|-----------|

--23. Show each customer's full name, total quantity of books ordered, and total amount spent. 
```
select concat(customers.first_name, ' ', customers.last_name) as Names, sum(orders.quantities) as total_quantity,
books.price * orders.quantities as Total_amount
from orders
join books on orders.book_id = books.book_id
join customers on customers.customer_id = orders.customer_id
group by customers.first_name, customers.last_name, books.price, orders.quantities;
```
names      |total_quantity|total_amount|
-----------|--------------|------------|
John Doe   |             1|     2200.88|
Mary Okello|             2|     3801.98|
Jane Smith |             1|     1500.35|
John Doe   |             2|     5201.78|
Paul Otieno|             3|     6602.64|

--24. List books that have never been ordered. 
```
select books.title, books.book_id
from books 
join orders on books.book_id = orders.book_id
group by books.title, books.book_id
having count(books.book_id) isnull;
```
title|book_id|
-----|-------|

--25. Find the customer who has spent the most in total (JOIN + GROUP BY + ORDER BY + 
--LIMIT). 
```
select concat(customers.first_name, ' ', customers.last_name) as Names, 
		sum(orders.quantities*books.price) as total_qantity
from orders
join books on orders.book_id = books.book_id
join customers on customers.customer_id = orders.customer_id
group by customers.first_name, customers.last_name
order by sum(orders.quantities*books.price) desc
limit 1;
```
names   |total_qantity|
--------|-------------|
John Doe|      7402.66|

--26. Write a query that shows, for each book, the number of different customers who have ordered it. 
```
select b.title, count(c.customer_id) as Number_of_customers
from orders o
left join books b  on b.book_id = o.book_id
left join customers c on c.customer_id = o.customer_id
group by b.title;
```
title                     |number_of_customers|
--------------------------|-------------------|
Data Analytics for Dummies|                  1|
Python for ML             |                  1|
Data Analytics Basics     |                  2|
Understanding SQL         |                  1|

--27. Using a subquery, list books whose total order quantity is above the average order quantity. 

```
SELECT 
    books.book_id,
    books.title
FROM 
    books
JOIN (
    SELECT 
        book_id,
        SUM(quantities) AS total_quantity
    FROM 
        orders
    GROUP BY 
        book_id
    HAVING SUM(quantities) > (
        SELECT AVG(quantities) from orders
    )
) orders ON books.book_id = orders.book_id;

```

title                |total_quantity|  
---------------------|--------------|
Data Analytics Basics|             4|


--28. Show the top 3 customers with the highest number of orders and the total amount they spent. 

```select concat(c.first_name, ' ', c.last_name) as Names, sum(o.quantities*b.price) as total_amount_spent, count(o.book_id)
from orders o
join books b on o.book_id = b.book_id
join customers c on c.customer_id = o.customer_id
group by c.first_name, c.last_name
order by sum(o.quantities*b.price) desc
limit 3;
```

names      |total_amount_spent|count|
-----------|------------------|-----|
John Doe   |           7402.66|    2|
Paul Otieno|           6602.64|    1|
Mary Okello|           3801.98|    1|
	