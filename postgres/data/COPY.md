# Копирование из файла

Можно загружать в таблицы данные из файлов с помощью команды `COPY`. При загурзке большого объема данных команда COPY как правило работает быстрее, чем INSERT, так как COPY оптимизирована для такого применения, хотя и менее гибка чем INSERT.

Пример:

```sql
COPY tablename FROM '/path/to/file';
```

Подразумевается, что файл доступен на компьтере, где работает серверный процесс, а не на клиенте, так как файл будет читаться непосредственно на сервере.