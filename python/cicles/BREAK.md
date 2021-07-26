# break

Оператор break вызывает немедленный выход из цикла.

```python
while True:
    name = input('Enter name: ')
    if name == 'stop':
        break
    age = input('Enter age: ')
    print('Hello', name, '=>', int(age) ** 2)
```
