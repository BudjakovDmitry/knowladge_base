# Цикл while

While - универсальная конструкция для итераций. Он многократно выполняет блок вложенных операторов до тех пор, пока услвие в заголовке является истиным. Когда условие становиться ложным, управление переходит на оператор, следующий за блоком while. Если проверка сразу же дает ложный результат, то тело цикла никогда не выполнится и оператор while пропускается.

## Общий формат

В самой сложной форме, оператор while выглядит так:

```
while check:
    operators
else:
    operators
```

Часть else является необязательной. Она выполняется, если управление покидает цикл, а оператор break не встретился.

```python
x = 'spam'
while x:
    print(x, end=' ')
    x = x[1:]
# spam pam am m
```