# Встроенные функции для работы с датой и временем

## current_date

Получение текущей даты. При вызове функции `current_date` скобки не используются.

```sql
SELECT current_date;
```

Эта функция часто используется для получения значения по умолчанию при вставке строк в таблицы.

## to_char

Функция `to_char` используется для преобразования формата вывода даты.

```sql
SELECT to_char(current_date, 'dd-mm-yyyy`);
```

## current_time

Используется для получения текущего времени

```sql
SELECT current_time;
```

Текущее время выводится с высокой точностью и дополняется числовым значением, соответствующим локальному часовому поясу, который установлен в конфигурационном файле сервера PosthreSQL.

Эта функция часто используется для получения значения по умолчанию при вставке строк в таблицы.

## current_timestamp

Функция служит для получения текущей временной отметки (дата и время).

```sql
SELECT current_timestamp;
```

Значения даты и времени вернуться с указанием часового пояса.

Эта функция часто используется для получения значения по умолчанию при вставке строк в таблицы.

## date_trunc

Используется для усечения временных отметок с той или иной точностью.

```sql
SELECT date_trunc('hour', current_timestamp);
```

## extract

Извлекает из временных отметок отдельные поля: год, месяц, день, число часов, минут, секунд и т.д.

Пример, как можно извлечь номер месяца.

```sql
SELECT extract('mon' FROM timestamp '1999-11-27 12:34:56')
```

## date_part

Извлекает данные из даты

Получить месяцы рождения сотрудников

```sql
SELECT first_name,
       last_name,
       date_part('month', birth_date)
FROM employees;
```
