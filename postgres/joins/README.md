# Соединения

## Внешние соединения

### right join

Правое внешнее соединение работает точно так же как и левое соединение, но в результирующую выборку попадут все записи из правой таблицы. По сути, можно вместо правого соединения применять всегда левое соединение, ставя слева ту таблицу, из который нужно достать все даныне. Поэтому правое соединение редко используется. Чаще делают левое соединение, располагая слева ту таблицу, из которой нужно забрать все данные.

Пример: чтобы получить такой же результат, как и при левом соединении, мы применяем правое соединение и меняем таблицы местами

```sql
SELECT countries.country, cities.city
FROM cities
    RIGHT JOIN countries ON countries.id = cities.country_id
ORDER BY countries.country;
```

country | city
--- | ---
Argentina | Boenos Aires
Germany | Berlin
Russia | Sochi
Russia | Moscow
Spain |
USA | New York
USA | Los Angeles

### Full outer join

Полное внешнее соединение - это объединение левого соединения и правого соединения. Забираются все записи из левой и правой таблиц и далее каждой записи из одной таблицы ищется соответствие из другой таблицы. Если записям из левой таблицы не находится совпадения, то они попадают в результирующий набор, а вместо данных из правой таблицы будут null. Для праврой таблицы аналогично: если записям не находится совпадение из левой таблицы, то данные будут дополнены значенями null и попадут в результирующий набор.

```sql
SELECT contries.country, cities.city
FROM contries
    FULL JOIN cities ON countries.id = cities.country_id
ORDER BY countries.country;
```

## Cross join

Cross join - это декартово произведение. Каждой записи из левой таблицы сопоставляются все записи из правой таблицы. Такой тип соединений на практике встречается редко.

```sql
SELECT countries.country, cities.city
FROM countries
    CROSS JOIN cities;
```

В cross join опускается часть ON, так как она просто не нужна.

## Self join

Self join чаще всего нужен, чтобы построить некую иерархию данных. По русски такое соединение называется _замкнутым соединением_. Для примера будем использовать таблицу employee.

```sql
CREATE TABLE employee
(
    id INTEGER PRIMARY KEY,
    first_name VARCHAR(255) NOT NULL,
    last_name VARCHAR(255) NOT NULL,
    manager_id INTEGER,
    FOREIGN KEY (managet_id) REFERENCES employee (id)
);
```

Особенность: manager_id - это внешний ключ, который ссылается на первичный ключ id в этой жее таблице. Это означает, что у работника может быть менеджер, который является такми же работником и у него может быть свой менеджер и т.д.

Заполняем таблицу данными.

id | first_name | last_name | manager_id
--- | --- | --- | ---
1 | Windy | Hays | NULL
2 | Ava | | Christensen | 1
3 | Hassan | Conner | 1
4 | Anna | Revers | 2
5 | Sau | Norman | 2
6 | Kelsie | Hays | 3
7 | Tory | Goff | 3
8 | Salley | Lester | 3

Теперь мы хотим вывести имена и фамилии работкиков, а рядом имя и фамилию менеджера этого работника.

```sql
SELECT e.first_name || ' ' || e.last_name AS employee,
       m.first_name || ' ' || m.last_name AS manager
FROM employee e
    LEFT JOIN employee m ON m.employee_id = e.manager_id
ORDER BY manager;
```

employee | manager
--- | ---
Anna Reeves | Ava Christensen
Sau Norman | Ava Christensen
Salley Lester | Hasan Conner
Kelsie Hays | Hassan Conner
Tory Goff | Hassan Conner
Hassan Conner | Windy Hays
Ava Christensen | Windy Hays
Windy Hays |

Синтаксически self join не отличается от обычного join, разница в смысле: таблица ссылается не на другую таблицу, а на саму себя.

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
