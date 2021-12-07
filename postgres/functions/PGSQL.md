# PL/pgSQL

Функции можно писать на языке PL/pgSQL. Это язык расширений для базового SQL.

Зачем вообще нужно писать функции на PL/pgSQL, если есть обычный SQL?

1. Создание переменных. В обычноных SQL функциях нельзя создавать переменные. В PL/pgSQL - можно.
2. Возможность создания циклов и развитой логики.
3. Возможность выбрасывать исключения.

Правила задания аргументов функции такие же как и в обычных SQL функциях: их можно помечать маркерами IN, OUT, ...

Тело функции должно начинаться с ключевого слова __BEGIN__, и заканичваться ключевым словом __END__ (BEGIN и END в данном случае не имеют отношения к транзакциям). После END можно ставить `;`, но это необязательно.

```sql
CREATE FUNCTION func_name([arg1, arg2, ...]) RETURNS date_type AS $$
  BEGIN
    --logic
  END;
$$ LANGUAGE plpgsql;
```

Отличия от обычных SQL функций

* Скалярные значения возвращаются через RETURN (вместо SELECT) или RETURN QUERY (в дополнение к SELECT).

__Пример 1__: скалярная функция, возвращающая единственное значение.

```sql
CREATE FUNCTION get_total_number_of_goods() RETURNS bigint AS $$
BEGIN
    RETURN sum(units_in_stock)
    FROM products;
END;
$$ LANGUAGE plpgsql;

SELECT get_total_number_of_goods();
```

__Пример 2__: вывести два значения через OUT параметры. Выведем максимальную и минимальную цену среди всех товаров.

```sql
CREATE FUNCTION get_price_boundaries(OUT max_price real, OUT min_price real) AS $$
BEGIN
  -- инициализируем OUT параметры max_price и min_price (это обычные переменные)
  -- в конце каждой строчки обязательно точка с запятой
  max_price := MAX(unit_price) FROM products;
  min_price := MIN(unit_price) FROM products;
END;
$$ LANGUAGE plpgsql;

SELECT * FROM get_price_boundaries();
```

Вместо `:=` можно использовать просто `=`. Разный синтаксис сделан для того, чтобы для операции присвоения и операции сравнения можно было использовать разные операторы.

Пример 2 работает, но он плох с точки зрения производительности: мы дважды выполниям запрос. Поэтому попробуем переписать его так, чтобы в одном запросе инициализировать обе переменные. Это делается с помощью ключевого слова __INTO__.

```sql
CREATE OR REPLACE FUNCTION get_price_boundaries(OUT max_price real, OUT min_price real) AS $$
BEGIN
  SELECT MAX(unit_price), MIN(unit_price)
  INTO max_price, min_price
  FROM products;
END;
$$ LANGUAGE plpgsql;
```

__Пример 3__: функция, которая принимает два числовых параметра и возвращает их сумму

```sql
CREATE FUNCTION get_sum(x int, y int, out result int) AS $$
BEGIN
  result := x + y;
END;
$$ LANGUAGE plpgsql;
```

__Пример 4__. Когда возвращаются множественные записи, нужно использовать конструкцию __RETURN QUERY__. Создадим функцию, которая принимает название страны и возвращает клиентов из этой страны

```sql
CREATE FUNCTION get_customers_by_country(customer_country varchar) RETURNS SETOF customers AS $$
BEGIN
  RETURN QUERY
  SELECT *
  FROM customers
  WHERE country = customer_country;
END;
$$ LANGUAGE plpgsql;

SELECT * FROM get_customers_by_country('USA');
```

## return

Чтобы выйти из функции (например в условии if) испольуется ключевое слово `RETURN;`

## Декларация переменных

В рамках тела функции могут быть объявлены и использованы переменные. Переменные объявляются в секции DECLARE. Эта секция идет после объявления сигнатуры функции. Внутри этой секции каждая переменная прописывается в отдельной строчке в формате: имя_переменной тип_переменной;

```sql
CREATE FUNCTION func_name([arg1, arg2, ...]) RETURNS data_type AS $$
DECLARE
  variable type; 
BEGIN
  --logic
END;
$$ LANGUAGE plpgsql;
```

__Пример 1__: функция, которая вычисляет площадь треугольника по трем сторонам.

```sql
CREATE FUNCTION get_square(a real, b real, c real) RETURNS real AS $$
DECLARE
  perimeter real;
BEGIN
  perimeter = (a + b + c) / 2;
  return sqrt(perimeter * (perimeter - a) * (perimeter - b) * (perimeter - c));
END;
$$ LANGUAGE plpgsql;

SELECT get_square(6, 6, 6);
```

__Пример 2__: функция, которая вычисляет среднюю цену товаров и среднюю цену умножает на некоторые коэффициенты.

```sql
CREATE FUNCTION calc_middle_price() RETURNS SETOF products AS $$
DECLARE
  avg_price real;
  low_price real;
  high_price real;
BEGIN
  SELECT AVG(unit_price) INTO avg_price
  FROM products;

  low_price = avg_price * 0.75;
  high_price = avg_price * 1.25;

  RETURN QUERY
  SELECT *
  FROM products
  WHERE unit_price BETWEEN low_price AND high_price; 
END;
$$ LANGUAGE plpgsql;
```
