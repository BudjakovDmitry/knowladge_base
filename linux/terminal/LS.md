# ls
ls - list directory content (вывести содержимое директории)
```
% ls
```

## Опции
* `-l` преобразует вывод в виде списка и добавит дополнительную информацию, например, размер каждой папки или файла.
* `-h` делает вывод более удобным для чтения человеком. Например, по умолчанию размер файлов и папок отображается в байтах. С данной опцией размер будет показан в более читабельном виде, например в килобайтах или мегабайтах.
* `-a` будут отображены все файлы и папки, включая скрытые. В Linux все скрытые файлы и папки начинаются с точки и по умолчанию не показываются командой ls.
* `-A` будут отображены все файлы и папки, включая скрытые, за исключением . и ..

## Аргументы
Аргументом является путь к директории. Если аргумент не укзать, то возвращается содержиоме текущей директории. Если аргумент задан, то возвращается содержимое той директории, которая указана в аргументе.
```
ls /var
```
