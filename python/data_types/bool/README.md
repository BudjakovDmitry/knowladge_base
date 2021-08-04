# Логические тип

Логические тип данных bool имеет значения True и False. 

Имена True и False являются экземплярами типа bool, который в свою очередь представляет собой подкласс встроенного целого типа int. Имена True и False ведут себя точно также, как целые числа 1 и 0 за исключением того, что они имеют специальную логику вывода - они отображают себя в виде слов True и False, а не цифр 1 и 0. В типе bool это достигается за счет переопределения форматов строк str и repr для двух указанных объектов.

```python
type(True)  # <class 'bool'>
isinstance(True, int)  # True
True == 1  # True
True is 1  # False
True + 4  # 5
```