# Соединения

## using

Using - это синтаксические сахар для написания кода, связанного с соединениями.

Рассмотрим запрос:

```sql
SELECT *
FROM orders
    JOIN order_details ON orders.order_id = order_details.order_id
    JOIN products ON order_details.product_id = products.product_id
    JOIN customers ON orders.customer_id = customers.customer_id
    JOIN employees ON orders.employee_id = employees.employee_id
WHERE ship_country = 'USA';
```

Довольно часто соединения проходят по столбцам, которые имеют одинаковые наименования: order_id, product_id, и т.д. В таких случаях вместо `ON orders.order_id = order_details.order_id` можно писать `USING(order_id)`. Запрос примет следующий вид:

```sql
SELECT *
FROM orders
    JOIN order_details USING(order_id)
    JOIN products USING(product_id)
    JOIN customers USING(customer_id)
    JOIN employees USING(employee_id)
WHERE ship_country = 'USA';
```

## natural join

```sql
SELECT *
FROM orders
    NATURAL JOIN employees;
```

Natural join работает аналогично с inner join, где соединение происходит по всем столбцам, которые проименованы одинаково. Однако данный код не явен. Гораздо правильнее указывать, по какому именно столбцу происходит соединение: так легче читать и отлаживать запросы. Помимо этого, если в будущем в таблицы будут добавлены столбцы с одинаковыми именами, то запрос станет возвращать другой результат.

Из вышесказанного делаем вывод: natural join не следует использовать вообще!
