# Типы данных

Типы данных объединены в группы, в рамках этих групп они имеют некоторые общие свойства, но также они имеют и различия.

* [Числовые типы](./NUMBERS.md)
* [Дата/время](./DATETIME.md)

## Приведения типов

Привеление типа позволяет преобразовать данные из одного типа в другой. PostgreSQL воспринимает две равносильные записи приведения типов:

* `CAST (expression AS type)`
* expression::type

Запись с `CAST` соответствует стандарту SQL, а вариант с `::` - историческое наследие PostgreSQL.
