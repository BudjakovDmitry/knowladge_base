# Проверка возникновения исключений

Чтобы проверить, что в каком-то коде возникает исключение, используется хэлпер *raises*.

```python
# content of test_sysexit.py
import pytest


def f():
    raise SystemExit(1)


def test_mytest():
    with pytest.raises(SystemExit):
        f()
```
