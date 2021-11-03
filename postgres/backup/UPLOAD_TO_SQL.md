# Выгрузка в SQL

## Создание дампа

Идея заключается в генерации текстового файла с командами SQL, которые при выполнении на сервере пересоздадут базу данных в том же самом состоянии, в котором она была на момент выгрузки. PostgreSQL предоставляет для этой цели вспомогательную программу pg_dump. Простейшее применение этой программы выглядит так:

```bash
pgdump db_name > dump_file
```

pg_dump выводит результат своей работы в поток стандартного вывода. Показанная выше команда создает текстовый файл, однако pg_dump может создавать файлы в других форматах, которые допускаю параллельную обработку, и более гибкое восстановление объектов.

Программа pg_dump является для PostgreSQL обычным клиентским приложением. Это означает, что вы можете выполнять процедуру резервного копирования с любого удаленного компьютера, если имеете доступ к нужной базе данных. pg_dump не использует для своей работы какие-то специальные привилегии. В частности, обычно ей требуется доступ на чтение всех таблиц, которые необходимо выгрузить, так что для копирования всей базы данных практически всегда ее нужно запускать с правами суперпользователя СУБД. Если у Вас нет достаточных прав для копирования всей базы данных, вы, тем не менее, можете сделать резервную копию той части базы, доступ к которой у Вас есть, используя такие параметры, как `-n schema` или `-t table`.

Указать, к какому серверу должна подключаться программа pg_dump, можно с помощью арументов командной строки `-h server` и `-p port`. По умолчанию в качестве сервера выбирается localhost или значение, указанное в переменной окружения `PGHOST`. Подобным образом, по умолчанию используется порт, заданный в переменной окружения PGPORT, а если она не задана, то порт, указанный по умолчанию при компиляции.

Как и любое другое клиентское приложение PostgreSQL, pg_dump по умолчанию будет подключаться к базе данных с именем пользователя, совпадающим с именем текущего пользователя операционной системы. Чтобы переопределить имя, либо добавьте парамер -U, либо установите переменную окружения `PGUSER`. pg_dump подключается к серверу через обычные механизмы проверки подлинности клиента.

Важное преимущество pg_dump в сравнении с другими методами резервного копирования состоит в том, что вывод pg_dump обычно можно загрузить в более новые версии PostgreSQL, в то время как резервная копия на уровне файлоыой системы и непрерывное архивирование жестко зависят от версии сервера. Также, только метод с применением pg_dump будет работать при переносе базы данных на другую машинную архитектуру, например, при переносе с 32-битной на 64-битную версию сервера.

Дампы, создаваемые pg_dump, являются внутренне согласованными, то есть, дамп представляет собой снимок базы данных на момент начала запуска pg_dump. pg_dump не блокирует другие операции с базой данных во время ее работы. Исключение составляют операции, которым нужна исключительная блокировка, как например, большинство форм команды ALTER TABLE).

## Восстановление дампа

Текстовые файлы, созданные pg_dump, предназначаются для последующего чтения программой psql. Общий вид команды для восстановления дампа:

```bash
psql db_name < dump_file
```

dump_file - файл, содержащий вывод команды pg_dump.

База данных, заданная параметром db_name, не будет создана данной командой, так что вы должны создать ее сами из базы template0 перед запуском psql (например, `create_db -T template0 db_name). Программа psql принимает параметры, указывающие сервер, к которому осуществляется подключение, и имя базы пользователя, подобно pg_dump. Дампы выгруженные не в текстовом формате, восстанавливаются утилитой pg_restore.

Перед восстановлением SQL-дампа все пользователи, или имели права на объекты должны уже существовать. Если их нет, при восстановлении будут ошибки пересоздания объектов с изначальными владельцами и/или правами.

По умолчанию, если происходит ошибка SQL, программа psql продолжает выполнения. Если же запустить psql с установленной переменной ON_ERROR_STOP, это поведение поменяется и psql завершится с кодом 3 в случае возникновения ошибки SQL.

```bash
psql --set ON_ERROR_STOP=on db_name < file_name
```

В любом случае, вы получите только частично восстановленную базу данных. В качестве альтернативы можно указать, что весь дамп должен быть восстановлен в одной транзакции, так что восстановление либо полностью выполнится, либо полностью отменится. Включить данный режим можно, передав в psql аргумент `-1` или `--single_transaction`. Выбирая этот режим следует помнить, что даже незначительная ошибка может привести к откату восстановления, которое могло продолжаться несколько часов. Однако, это все же может быть предпочтительней, чем вручную вычищать сложную базу данных, после частично восстановленного дампа.

Благодаря способности pg_dump и psql писать и читать в каналы ввода/вывода, можно скопировать базу данных непосредственно с одного сервера на другой, например:

```bash
pg_dump -h host1 db_name | psql -h host2 db_name
```

Дампы, которые выдает pg_dump, соержат определения относительно template0. Это означает, что любые языки, процедуры и т.п., добавленные в базу через template1, pg_dump также выгрузит в дамп. Как следствие, если при восстановлении вы используете модифицированный template1, вы должны создать пустую базу данных из template0.

После восстановления резервной копии имеет смысл запустить ANALYZE для каждой базы данных, чтобы оптимизатор запросов получил полезную статистику.

## pg_dumpall

Программа pg_dump выгружает только одну базу данных в один момент времени и не включает в дамп информацию о ролях и табличных пространствах (так как это информация уровня кластера, а не самой базы данных). Для удобства создания дампа всего содержимого кластера баз даных предоставляется программа pg_dumpall, которая делает резервную копию всех баз данных кластера, а также сохраняет данные уровня кластера, такие как роли и определения табличных простарнств. Пример простого использования это команды:

```bash
pg_dumpall > dump_file
```

Полученную копию можно восстановить с помощью psql

```bash
psql -f dump_file postgres
```

В качестве начальной базы данных можно указать имя любой существующей базы, но если вы загружаете дамп в пустой кластер, обычно нужно использовать postgres.

Восстанавливать дамп, который выдала pg_dumpall, всегда необходимо с правами суперпользователя, так как они требуются для восстановления информации о ролях и табличных пространствах. Если вы используете табличные пространства, убедитесь, что пути к табличным пространствам в дампе соответствуют новой среде.

pg_dumpall выдает команды, которые заново создают роли, табличные пространства и пустые базы данных, а затем вызывает для каждой базы pg_dump. Таким образом, хотя каждая база данных внутренне будет согласованной, состояние разных баз не будет синхронным.

Только глобальные данные кластера можно выгрузить, передав pg_dumpall ключ `--globals-only`. Это необходимо, чтобы полностью скопировать кластер, когда pg_dump выполняется для отдельных баз данных.