# Установка

## Установка через менеджер пакетов

Установить nginx на Ubuntu достаточно просто.

```bash
apt-get install nginx
```

Сразу после установки Nginx автоматически запустится и начнет принимать входящие соединения на порту 80. Если мы зайдем через браузер на сервер (или localhost, если nginx развернут локально), то увидим приветственную страницу Nginx.

Nginx ставится в директорию `/etc/nginx/`. Там же будут располагаться файлы конфигурации.
