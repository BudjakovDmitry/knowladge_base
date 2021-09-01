# Nginx

## Описание

У nginx есть один главный и несколько рабочих процессов. Основная задача главного процесса - чтение и проверка конфигурации и управление рабочими процессами. Рабочие процессы выполняют фактическую обработку запросов. Количество рабочих процессов задается в конфигурационном файле и может быть фиксированным для данной конфигурации или автоматически устанавливаться равным числу доступных процессорных ядер. Для эффективной работы nginx использует модель, основанную на событиях операционной системы.

Как работает nginx и его модули определяется в конфигурационном файле. По умолчанию, конфигурационный файл называется nginx.conf и расположен в каталоге /usr/local/nginx/conf, etc/nginx или /usr/local/etc/nginx.