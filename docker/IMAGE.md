# Образ

Образы - это неизменные шаблоны, которые используются для создания одинаковых контейнеров. Образы Docker можно сравнить с чертежами, на основе которых создаются изделия.

В образе содержится образ базовой операцинной системы, код приложения, библиотеки, от которого оно зависит. Все это скомпановано в виде единой сущности, на основе котрой можно создать контейнер.

Готовые образы хранятся в докер-реестрах (registry). Они имеют хэш, имя и тег. Теги используются для уточнения, что именно лежит внутри образа. Например образ Centos может содержать версию 6 или 7. Чтобы понять, какая версия лежит внутри, указывается тег. Также можно собрать свой образ локально.

Для создания образа используется команда `docker build`.

Образ имеет слоеную структуру. Он собирается по инструкциям из Dockerfile и, как правило, каждая инструкция создает новый слой. Слои могут переиспользоваться.

Образы создаются (билдятся) по инструкции, которые расположены в Докерфайле.
