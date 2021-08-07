# Заглушки переменных окружения

Ингода для тестовых целей нужно безопасно изменить или удалить значения переменных окружения. Фикстура monkeypatch позволяет сделать это при помощи методов `setenv` и `delenv`.

Пример кода, который будет тестироваться:

```python
import os


def get_os_user_lower():
    username = os.getenv("USER")
    if username is None:
        raise OSError("USER environment is not set.")

    return username.lower()
```

В этом сценарии два возможных пути: переменная USER задана; переменной USER не существует. Оба пути могут быть безопасно протестированы без воздействия на текущее окружение.

```python
import pytest


def test_upper_to_lower(monkeypatch):
    monkeypatch.setenv("USER", "TestingUser")
    assert  get_os_user_lower() == "testinguser"


def test_raise_exception(monkeypatch):
    monkeypatch.delenv("USER", raising=False)
    with pytest.raises(OSError):
        _ = get_os_user_lower()
```

Логика создания и удаление переменной окружения может быть перенесена в фикстуру и переиспользована в разных тестах.

```python
import pytest


@pytest.fixture
def mock_env_user(monkeypatch):
    monkeypatch.setenv("USER", "TestingUser")


@pytest.fixture
def mock_env_missing(monkeypatch):
    monkeypatch.delenv("USER", raising=False)


def test_upper_to_lower(mock_env_user):
    assert get_os_user_lower() == "testinguser"


def test_raise_exception(mock_env_missing):
    with pytest.raises(OSError):
        _ = get_os_user_lower()
```
