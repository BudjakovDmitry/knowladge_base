# Циклы

## for

```bash
for var in values_list
do
    # действия
    # на каждой следующей итерации переменная
    # принимает следующее значение из списка значений
done
```

Значения в списке указываются через пробел

```bash
for i in 1 2 3 4 5
do
    file_name=file${i}.txt
    if [[ -e $file_name ]]
    then
        continue
    fi
    echo "Creating file $file_name"
    touch $file_name
done
```

Компактная запись

```bash
for i in 1 2 3 4 5; do
# ...
```

## while

```bash
while [[ condition ]]
do
    # действия, пока условие истино
done
```

```bash
again=yes
while [ "$again" = "yes"  ]
do
    echo "Please enter name:"
    read name
    echo "The name is $name"

    echo "Continue? (yes/no)"
    read again
done
```

## break, continue

```bash
break - прервать выполнение цикла
continue - перейти к следующей итерации цикла
```
