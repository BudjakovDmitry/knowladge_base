# Файл pg_hba.conf

Аутентификация клиентов управляется конфигурационным файлом, который называется `pg_hba.conf` и располагается в каталоге с данными кластера баз данных.

HBA - host-based authentication

Файл pg_hba.conf со стандартным содержимым создается командой initdb при инициализации катаога с данными. Однако его можно разместить в любом другом месте.

Обычный формат файла pg_hba.conf представляет собой набор записей, по одной в строке. Пустые строки игнорируются, как и любой текст комментария после знака #. Записи на продолжаются на следующей строке. Записи состоят из некоторого количества полей, разделенных между собой пробелом и/или символом табуляции. В полях могут быть использованы пробелы, если они взяты в кавычки. Если в кавычки берется какое-либо зарезервированное слово в поле базы данных, пользователя или адресации (например, all или replication), то слово теряет свое особое значение и просто обозначает базу данных, пользователя или сервер с данным именем.

Каждая запись обозначает тип соединения, диапазон IP-адресов клиента (если он соотносится с типом соединения), имя базы данных, имя пользователя, и способ аутентификации, который будет использован для соединения в соответствии с этими параметрами. Первая запись с соответствующим типом соединения, адресом клиента, указанной базой данных и именем пользователя применяется для аутентификации. Если выбрана запись и аутентификация не прошла, последующие записи не рассматриваются. Если же ни одна из записей не подошла, в доступе будет отказано.

Записи могут иметь следующие форматы:

```
local  base  user  auth-method [auth-params]
host   base  user  address  auth-method [auth-params]
```

`local`

Управляет подключениями через Unix-сокеты. Без подобной записи подключения через Unix-сокеты невозможны.

---

`host`

Управляет подключениями, устанавливаемыми по TCP/IP. Запись host соответствует подключениям с SSL и без SSL, а также подключениям, защищенным механизмами GSSAPI и не защищеннымы GSSAPI.