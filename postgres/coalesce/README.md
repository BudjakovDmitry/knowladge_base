# coalesce

Данная функция принимает n-ое количество аргументов и возвращает первый аргумент, который не NULL. Если все аргументы равны NULL, то coalesce вернет NULL. Чаще всего используется, чтобы подставить какое-то начение вместо NULL: в качестве первого аргумента мы передаем тот, от которого мы ожидаем, что он может быть NULL, а в качестве второго аргумента передаем некоторый литерал.

```sql
coalesce(arg1, arg2, ...)
```

```sql
SELECT first_name,
       last_name,
       COALESCE(auto_number, 'no') auto_number1
FROM employees;
```

Пример использования COALESCE внутри блока order by: вывести список сотрудников,отсортировав по фамилии и городу, а если город не указан, то по стране

```sql
SELECT first_name, last_name, city, country
FROM employees
ORDER BY last_name, coalesce(city, country);
```
