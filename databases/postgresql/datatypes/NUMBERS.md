# Числовые типы

Группа числовых типов состоит из:

* целочисленные типы
   * smallint
   * integer
   * bigint
* числа фиксированной точности
* числа с плавающей точкой
* последовательные типы (serial)

## Целочисленные типы

Если атрибут таблицы имеет целочисленный тип, то он позволяет хранить только целочисленные данные.

Включает в себя представителей: `smallint`, `integer`, `bigint`. Перечисленные тодтипы различаются по количеству байтов, выделяемых для хранения данных. В PostgreSQL существуют псевдонимы для этих стандартизированных имен типов: `int2`, `int4` и `int8`. Число байт отражается в имени типа

Название | Псевдоним | Занимаемая память (байт)
--- | --- | ---
smallint | int2 | 2
integer | int 4 | 4
bigint | int8 | 8

При выборе конкретного целочисленного типа принимают во внимание диапазон допустимых значений и затраты памяти. Зачастую тип `integer` считается оптимальным выбором с точки зрения достижения компромисса между этими показателями.

## Числа с фиксированной точностью

Числа с фиксированной точностью представлены двумя типами:

* `numerec`
* `decimal`

Эти типы идентичны по своим возможностям.

### numeric

Для задания значения этого типа используются два базовых понятия: *масштаб (scale)* и *точность (precision)*. Масштаб показывает число значащих цифр, стоящих справа от десятичной точки. Точность указывает общее число цифр. Например, у числа 12.3456 точность составляет 6 цифр, а масштаб - 4 цифры.

```sql
numeric(точность, масштаб)
numeric(6, 4)
```

Главное достоинство этих чисел - обеспечение точных результатов при выполнении вычислений, когда это, конечно, возможно в принципе. Это оказывается возможным при выполнении сложения, вычитания и умножения. Однако точность достигается за счет замедления вычислений по сравнению с целочисленными типами и типами с плавающей точкой. При этом для хранения числа затрачиваетсябольше памияти, чем в случае целых чисел.

Числа типа numeric могут хранить 131 072 цифру до десятичной точки и 16 383 после десятичной точки.

Данный тип следует выбирать для хранения денежных сумм, а также в других случаях, когда требуется гарантировать точность вычислений.

## Числа с плавающей точкой.

Представителями являются типы `real` и `double precision`. Они представляют собой реализацию стандарта IEEE "Standard 754 for Binary Floating-Point Arithmetic".

Название | Диапазон значений | Точность
--- | --- | ---
real | от 1E-37 до 1E+37 | Не меньше 6 десятичных цифр
double precision | от 1E-307 до 1E+308 | Не меньше 15 десятичных цифр

А также поддерживаются специальные значения:

* Infinity (бесконечность)
* -Infinity (отрицательная бесконечность)
* NaN (не число)

При попытке записать слишком большое или слишком маленькое число, будет генерироваться ошибка. Если точность вводимого числа выше допустимой, то дудет иметь место округление значения. При вводе очень маленьких чисел, которые невозможно представить значениями, отличными от нуля, будет генерироваться ошибка потери значимости, или исчезновения значщих разрядов (an underflow error).

Сравнение двух типов с плающей точкой на предмет равенства может привести к неожиданным результатам:

```sql
SELECT 0.1::real * 10 = 1.0::real;

 ?column?
----------
f
```

### float

PostreSQL поддерживает тип данных `float`, определенный в стандарте SQL. При объявлении типа может ипользоваться параметр `float(p)`. Если его значение лежит в диапазоне от 1 до 24, то это будет равносильно использованию типа `real`, а если значение лежит в диапазоне от 25 до 53, то это будет равносильно использованию типа `double precision`. Если при объявлении типа парамер не используется, то это равносильно использованию типа `double precision`.

## serial

Это фактически не настоящий тип, а просто удобная замена группы SQL-команд. serial удобен в тех случаях, когда требуется в какой-то столбец вставлять уникальные целые значения, например, значения суррогатного первичного ключа.

Синтаксис созданя serial столбцов:

```sql
CREATE TABLE table_name (column_name serial);
```

эта команда эквивалентна группе команд

```sql
CREATE SEQUENCE table-name_column-name_seq;
CREATE TABLE table-name
(
   column-name integer NOT NULL
      DEFAULT nextval('table-name_column-name_seq')
);
ALTER SEQUENCE table-name_column-name_seq
   OWNED BY table-name.column-name;
```