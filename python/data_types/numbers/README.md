# Числа

Базовый набор включает в себя:

* [целые числа](./INTEGERS.md) - числа без дробной части
* [вещественные числа](./FLOATS.md). Грубо говоря - это числа с десятичной точкой.
* коплексные числа
* числа с фиксированной точностью
* рациональные числа

## Операции с числами
Числа поддерживают набор самых обычных математических операций:

* `+` - сложение
* `*` - умножение
* `**` - возведение в степень

```python
123 + 222
# 345

1.5 * 4
# 6.0

2 ** 100
# 1267650600228229401496703205376
```

Целые числа обеспечивают неограниченную точность для представления больших значений.

## Станндартная библиотека для работы с числами

* [math](../../stl/MATH.md) - содержит набор математических функций
* [random](../../stl/RANDOM.md) - генератор случайных чисел и функция случайного выбора