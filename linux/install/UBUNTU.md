# Установка программ через терминал на Ubuntu
Для установки, удаления, обновления программ используется команда `apt-get`. Опции определяют тип действия (устпновить, удалить, обновить). В качестве аргумента указывается имя программы.

## Опции
### Установка
**install** - установить программу

```bash
sudo apt-get install filezilla 
```

### Удаление
**remove** - удалить программу

```bash
sudo apt-get remove filezilla 
```

### Обновление
**update** - обновление ссылок на пакеты

```bash
sudo apt-get update
```

**upgrade** - обновление установленных пакетов

```bash
sudo apt-get upgrade
```

**install --only-upgrade** - обновление отдельной программы

```bash
sudo apt-get install --only-upgrade filezilla 
```
