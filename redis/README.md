# Redis

## Использование в различных ОС

### Windows

Redis официально не поддерживает Windows.

## *nix и MacOS

### Из исходников

Пример для версии 6.2.6

```bash
$ wget https://download.redis.io/release/redis-6.2.6.tar.gz
$ tar xzf redis-6.2.6.tar.gz
$ cd redis-6.2.6
$ make
```

Исполняемые файлы будут находится в директории `src`.

## Запуск и подключение

Redis имеет множество исполняемых файлов.

Для запуска сервера служит команда

```bash
redis-server
```

Выйти из клиента Redis

```bash
>quit
```


