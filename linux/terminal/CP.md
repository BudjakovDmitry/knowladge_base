# cp
Копирует файл или директорию.

```
% cp path/to/file other/path/to/new_file
```

В примере выше файл, расположенный в `path/to/file` будет скопирован в `other/path/to/new_file`

## Опции
- `-r` если необходимо копировать директорию

   `% cp -r path/to/dir1 path/to/dir2`

## Аргументы
Первым аргументом идет путь до файла, который нужно скопировать. Вторым аргументом идет путь, куда нужно скопировать. Если в качестве второго пути указать директорию, то файл будет скопирован в эту директорию не изменив своего имени.
