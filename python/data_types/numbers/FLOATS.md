# Вещественные числа
## Способы записи
Запись целой и дробной частей через разделитель: точку.

```python
0.5
```

Экспоненциальная форма записи

```python
5e-1  # 5 умножается на 10 в степени -1
# 0.5
1234e-2
# 12.34
1234e2
# 123400.0
```

С вещественными числами работают все стандартные арифметические операции:

**разность**

```python
4.0 - 2.4
# 1.6
```

**деление**

```python
5 / 2
# 2.5
```

Результатом деления всегда будет вещественное число

```python
6 / 3
# 2.0
```

При работе с вещественными числами возможна потеря точности. Иногда это очевидно, напрмер, дробь 1/3 в математике представляется бесконечной десятичной дробью. В компьютере число будет округлено.

```python
1 / 3
0.3333333333333333
```

Иногда потеря точности происходит там, где это неочевидно

```python
0.3 + 0.3 + 0.3
# 0.8999999999999999
```

При работе с нецелыми числами следует всегда учитывать, что существует потеря точности.