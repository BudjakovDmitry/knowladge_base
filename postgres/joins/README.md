# Соединения

Запросы на выборку данных могут обращаться сразу к нескольким таблицам, или обращаться к одной и той же таблице так, что одновременно будут обрабатываться разные наборы ее строк. Такой запрос называется __соединением__ (`JOIN`).

Существует несколько типов соединений:

* INNER JOIN - внутреннее соединение
* LEFT JOIN (LEFT OUTER JOIN) - левое внешнее соединение
* RIGHT JOIN (RIGHT OUTER JOIN) - правое внешнее соединение
* FULL JOIN - полное соединение
* CROSS JOIN - декартово произведение
* SELF JOIN - соединение таблицы на саму себя

Рассмотрим, как работают разные типы соединений на примере двух таблиц:

__contries__

id | contry
--- | ---
1 | Argentina
2 | Spain
3 | Germany
4 | Russia
5 | USA

__cities__

id | city | country_id
--- | --- | ---
1 | Moscow | 4
2 | Sochi | 4
3 | Berlin | 3
4 | Buenos Aires | 1
5 | New York | 5
6 | Los Angeles | 5

При работе с соединениями можно улышать такие выражения как _левая_ таблица и _правая_ таблица. Когда составляется выражение соединения, первая таблица в этом выражении считается левой, а вторая - правой. Как правило, нично не мешает поменять эти таблицы метами и соединять вторую таблицу с первой. В этом случае левой будет вторая таблица, а правой - первая. В некоторых случаях то, какая таблица где находится может влиять на результат запроса.

## Внутренние соединения

Допустим, мы хотим посмотреть все города и страны, в которых эти города располагаются.

```sql
SELECT countries.country, cities.city
FROM cities
    INNER JOIN countries ON countries.id = cities.country_id
ORDER BY countries.country;
```

Вместо INNER JOIN можно писать просто JOIN

```sql
SELECT countries.country, cities.city
FROM cities
    JOIN countries ON countries.id = cities.country_id;
```

Имена столбцов указываются вместе с именем таблицы: table_name.column_name. Так сделано потому что в соединяемых таблицах могут быть столбцы с одинаковыми именами. Чтобы СУБД понимала, какой именно столбец взять, его конкретизируют через имя таблицы. Если все имена столбцов уникальны, то можно писать просто их имена: `SELECT country, city`, однако, хорошим стилем считается всегда конкретизровати имена столбцов, чтобы запрос не сломался, если в будущем будут добавлены новые столбцы с повторяющимися именами.

Для inner join не имеет значения, какая таблица будет левой, а какая - правой.

Результат будет таким:

country | city
--- | ---
Argentina | Buenos Aires
Germany | Berlin
Russia | Moscow
Russia | Sochi
USA | New York
USA | Los Angeles

В результирующем наборе нет страны Spain. Так получилось потому что в таблице cities нет ни одной записи, ссылающейся на эту страну. При внутреннем соединении, в результирующий набор попадают только те строки, в которых есть соответствующая запись во второй таблице. Все остальные строки отсекаются.

А вот страны Russia и USA доблируются по два раза. Это происходит потому, что на каждую из этих записей в таблице country ссылается по две записи из таблицы cities.

Внутренее соединение можно записать используя другой синтаксис:

```sql
SELECT country, city
FROM countries, cities
WHERE countries.id = cities.country_od
ORDER BY country;
```

## Внешние соединения

К внешним соединениям относятся LEFT JOIN и RIGHT JOIN.

### left join

Пример

```sql
SELECT country, city
FROM countries
    LEFT JOIN cities ON cities.country_id = countries.id
ORDER BY countries.country;
```

Результат:

country | city
--- | ---
Argentina | Boenos Aires
Germany | Berlin
Russia | Sochi
Russia | Moscow
Spain |
USA | New York
USA | Los Angeles

В результирующую выборку добавилась страна Spain, которой не соответствует никакой город (city = NULL). При левом внешнем соединении из левой таблицы в результирующий набор попадают абсолютно все записи. Если записи из левой таблицы соответствует запись (записи) из правой таблицы, то происходит обычное соединение, как при внутреннем соединении. Те строки из левой таблицы, которым нет соответствия в правой таблице, все равно попадают в результирующий набор, а вместо значений из правой таблицы подставляется NULL.

В частном случае, когда всем записям в левой таблице соответствуют записи в правой таблице, результат LEFT JOIN не будет отличаться от резултата INNER JOIN.

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
