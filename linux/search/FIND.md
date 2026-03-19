# find

## Аргументы

* `find -path "<path>"` - найти указанный путь

   `find -path "*images*/*jpg"`

* `find -mindepth <number>` - искать начиная с заданного числа уровней вниз. Уровень 1 - это текущая директория.

   `find -mindepth 3 -name "*.jpg"`
