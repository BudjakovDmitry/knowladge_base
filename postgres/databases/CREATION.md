# Создание базы данных

Для создания БД нужно выполнить в терминале команду

```bash
createdb dbname
```

dbname - имя создаваемой БД

PostgreSQL позволяет создавать сколько угодно баз данных. Имена баз данных должны начинаться с буквы и бвть не длиннее 63 символов.

По умолчанию многие утилиты предполагают, что имя БД будет совпадать с именем текущего пользователя. Чтобы создать БД с именем текущего пользователя нужно ввести:

```bash
createdb
```