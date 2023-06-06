# Функции

## Возврат множества строк из функции

Существует разный синтаксис

### RETURNS SETOF data_type

`RETURNS SETOF data_type` - возвращается набор n-скалярных значений типа data_type.

__Пример__: вернуть средние цены по каждой категории продуктов. В результате получим таблицу из одного столбца, в котором содержатся средние цены по каждой категории продуктов.

```sql
CREATE FUNCTION get_avg_prices_by_prod_categories()
    RETURNS SETOF double precision AS $$
  SELECT AVG(unit_price)
  FROM products
  GROUP BY category_id
$$ LANGUAGE SQL;

SELECT * FROM get_avg_prices_by_categories() AS avg_prices;
```

### RETURNS SETOF table

`RETURNS SETOF table` - вернуть все столбцы из таблицы или пользовательского типа. table - это наименование таблицы или пользовательского типа.

__Пример__: функция, которая принимает на вход страну и возвращает клиентов из этой страны.

```sql
CREATE OR REPLACE FUNCTION get_customers_by_country(customer_country varchar)
    RETURNS SETOF customers AS $$

   --обязатенльно использовать SELECT *
   --SELECT customr_id, name, ... работать не будет
   SELECT *
   FROM customers
   WHERE country = customer_country
$$ LANGUAGE SQL;

SELECT * FROM get_customers_by_country('USA');
SELECT company_name, contact_name FROM get_customers_by_country('USA');
```

### RETURNS SETOF record

`RETURNS SETOF record` - используется, когда типы колонок в результирующем наборе заранее неизвестны. Данным вариантом стоит пользоваться только в случае необходимости. Использование этого подода приводит к тому, что мы работаем с результирующим набором, как с анонимным типом, что снижает удобство пользования.

__Пример__: функция, возвращающая суммарную стоимость товаров и среднюю стоимость товаров в каждой категории. В этом примере используются OUT параметры.

```sql
CREATE FUNCTION get_avg_prices_by_categories(OUT sum_price real, OUT agv_price float8)
    RETURNS SETOF RECORD AS $$
  SELECT SUM(unit_price), AVG(unit_price)
  FROM products
  GROUP BY category_id
$$ LANGUAGE SQL;

SELECT * FROM get_avg_prices_by_categories();
```

__Пример 2__: SETOF record используется без OUT параметров.

```sql
CREATE FUNCTION get_avg_prices_by_categories() RETURNS SETOF RECORD AS $$
  SELECT SUM(unit_price), AVG(unit_price)
  FROM products
  GROUP BY category_id
$$ LANGUAGE SQL;
```

Проблемы начаниются, когда мы попытаемся вызвать такую функцию:

```sql
SELECT * FROM get_avg_prices_by_categories();  --ОШИБКА
SELECT sum_price FROM get_avg_prices_by_categories();  --ОШИБКА
```

Единственный способ получить данные из такой функции - указать аргументы и их типы во время выборки

```sql
SELECT * FROM get_avg_prices_by_categories() AS (sum_price real, avg_price float8);
```

### RETURNS TABLE (column_name date_type, ...)

`RETURNS TABLE (column_name data_type, ...)` - то же, что и SETOF table, но имеем возможность явно указать возвращаемые столбцы.

__Пример__: функция, которая принимает входным аргументом страну и возвращает клиентов из этой страны.

```sql
CREATE FUNCTION get_customers_by_country(customer_country varchar)
    RETURNS TABLE(code char, company_name varchar) AS $$
  SELECT customer_code, company_name
  FROM customers
  WHERE country = customer_country
$$ LANGUAGE SQL;

SELECT * FROM get_customers_by_country('USA');
```

### OUT-параметры

Возврат через out-параметры. В премерах выше, в объявлении функции была опущена часть RETURNS SETOF record. Если запрос возвращает одну строку, то RETURNS SETOF record можно не писать.  Возврат через out-параметры - это возврат одной строки с несколькими значениями. Если нужно вернуть несколько строк, то нужно писать RETURNS SETOF record.
