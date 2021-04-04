# grep
Команда используется для поиска подстроки в файле.
```bash
grep "<string>" <file>
```

Найти слово "hello" в файле file.txt
```bash
grep "hello" file.txt
```

Посчитать количество строк, которые содержат указанную подстроку в файле
```bash
grep -c "<string>" <file>
```

```bash
grep -c "123" file.txt
```

Найти подстроку во всех файлах в папке
```bash
grep -r "<substring>" <folder>
```

```bash
grep -r "world" ~/
```
