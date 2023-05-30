# Функции

## Описание

* Функции состоят из набора утверждений, возвращая результат последнего
* могут содержать SELECT, INSERT, UPDATE, DELETE (CRUD)
* не могут содержать COMMIT, SAVEPOINT (TLC), VACUUM (utility). Однако функции сами по себе транзакционны: если при выполнении функции произошла какая-то ошибка, то все что было сделано внутри функции будет автоматически откачено

Функции делятся на:

* SQL-функции
* процедурные (PL/pgSQL - основной диалект)
* серверные функции (написанные на C)
* собственные С-функции

## Синтаксис

```sql
CREATE [OR REPLACE] FUNCTION func_name([arg1, arg2, ...]) RETURNS data_type AS $$
  --logic
$$ LANGUAGE lang
```

CREATE OR REPLACE, модифицирует уже существующую функцию с таким же наименованием

Аргументов может и не быть.

data_type - указание типа результата, который возвращает функция

lang - указание языка: SQL или PL/pgSQL

Удаление функции:

```sql
DROP FUNCTION function_name;
```

### Примеры

__Пример 1__: простейшая функция, которая не принимает никаких аргументов и ничего не возвращает.

Предположим, мы хотим проиндексировать зарплату тем сотрудникам, кто получает меньше 100000, и увеличить ее на 5%. Для этого нам понадобаиться выполнить такой запрос

```sql
UPDATE employees
SET salary = salary * 1.05
WHERE salary < 100000;
```

Данный запрос можно оформить в виде функции, чтобы со стороны клиентского приложения не нужно было писать SQL-запрос, а просто произвести вызов функции.

```sql
CREATE OR REPLACE FUNCTION index_salary() RETURNS void AS $$
  UPDATE employees
  SET salary = salary * 1.05
  WHERE salary < 100000
$$ LANGUAGE SQL;
```

Данная функция не принимает никаких аргументов. Она личего не возвращает, поэтому мы пишем RETURNS void

Вызов созданной функции:

```sql
SELECT index_salary();
```

__Пример 2__: скалярная функция - функция, которая возвращает какое-то единственное значение.

Создадим функцию, которая возвращает суммарное количество всех товаров.

```sql
CREATE FUNCTION get_total_number_of_goods() RETURNS bigint AS $$
  SELECT SUM(units_in_stock)
  FROM products
$$ LANGUAGE SQL;

--вызов функции
SELECT get_total_number_of_goods() as total_goods;
```

## Аргументы функции

В функции можно передавать аргументы. Каждый аргумент помечается определенным маркером. По умолчанию аргументы неявно помечаются маркером __IN__, что означает, что аргумент является входящим.

__Пример 1__: функция, которая принимает параметр - имя продукта и возвращает его цену.

```sql
CREATE FUNCTION get_product_price_by_name(prod_name varchar) RETURNS real AS $$
  SELECT unit_price
  FROM products
  WHERE product_name = prod_name
$$ LANGUAGE SQL;

--вызов функции
SELECT get_product_proce_by_name('Chocolide') AS price;
```

Маркером __OUT__ помечаются исходящие аргументы. В эти аргументы можно записывать значения и эти значения клиентский код получит в качестве результата.

__Пример 2__: функция, которая возвращает ценовой диапазон: минимальную и максимальную цены среди всех товаров, которые есть у нас в базе. Для этого мы будем использовать два OUT аргумента.

```sql
CREATE FUNCTION get_price_boundaries(OUT max_price real, OUT min_price real) AS $$
  SELECT MAX(unit_price), MIN(unit_price)
  FROM products
$$ LANGUAGE SQL;
```

Важно, чтобы внутри SELECT значения шли в той же последовательности, что и аргументы с типом OUT, так как аргументы и значения сопоставляются по номеру позиции.

Вызвать функцию можно немного по-разному

```sql
--вариант 1
--получим в результате одно поле с типом record: (263.5,2.5)
SELECT get_price_boundaries();

--вариант 2
--получим в результате два поля: min_price и max_price с соответствующими атомарными значениями
SELECT * FROM get_price_boundaries();

--можно выбрать конкретные столбцы
SELECT max_price FROM get_price_boundaries();
```

__Пример 3__: функция, в которой присутствуют как in, так и out параметры

```sql
CREATE FUNCTION get_price_bounderies_by_discontinuity(is_discontinued int, OUT max_price real, OUT min_price real) AS $$
  SELECT MAX(unit_price), MIN(unit_price)
  FROM products
  WHERE discontinued = is_discontinued
$$ LANGUAGE SQL;

--вызов
SELECT * FROM get_price_boundaries_by_discontinuity(1);
```

Аргументам можно присвоить значение по умоланию при помощи ключевого слова `DEFAULT value`.

```sql
CREATE FUNCTION get_max_price(id_discontinued int DEFAULT 1, out max_price real) as $$
  SELECT MAX(unit_price)
  FROM products
  WHERE discontinued = is_discontinues
$$ LANGUAGE SQL;
```

Аргументы, помеченные __INOUT__ могут использоваться как входящие, так и исходящие. Однако на практике таким маркером лучше не пользоваться, так как это усложняет отладку кода. Хорошей практикой считается разделение входящих и исходящих аргументов.

Маркером __VARIADIC__ помечается массив входящих параметров

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
