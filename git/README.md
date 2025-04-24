 # Git

## Содержание
1. [Основы](#основы)
2. [Работа с ветками](#работа-с-ветками)
3. [Отмена изменений](#отмена-изменений)
4. [Слияние](#слияние)
5. [Работа с удаленным репозиторием](#работа-с-удаленным-репозиторием)
6. [Рабочие процессы](#рабочие-процессы)
7. [Полезные команды](#полезные-команды)

## Основы

### Инициализация репозитория
```bash
# Создание нового репозитория
git init

# Клонирование существующего репозитория
git clone <url>
```

### Базовые команды
```bash
# Проверка статуса
git status

# Добавление файлов в индекс
git add <file>
git add .

# Создание коммита
git commit -m "Сообщение коммита"

# Просмотр истории коммитов
git log
```

## Работа с ветками

### Создание и переключение
```bash
# Создание новой ветки
git branch <branch-name>

# Переключение на ветку
git checkout <branch-name>

# Создание и переключение на новую ветку
git checkout -b <branch-name>
```

### Управление ветками
```bash
# Список веток
git branch

# Удаление ветки
git branch -d <branch-name>

# Принудительное удаление ветки
git branch -D <branch-name>
```

## Отмена изменений

### Отмена в рабочей директории
```bash
# Отмена изменений в файле
git checkout -- <file>

# Отмена всех изменений
git checkout -- .
```

### Отмена в индексе
```bash
# Удаление файла из индекса
git reset <file>

# Отмена всех изменений в индексе
git reset
```

### Отмена коммитов
```bash
# Отмена последнего коммита
git reset --soft HEAD~1

# Отмена последнего коммита с удалением изменений
git reset --hard HEAD~1

# Отмена конкретного коммита
git revert <commit-hash>
```

## Слияние

### Merge
```bash
# Слияние ветки в текущую
git merge <branch-name>

# Слияние с отключением fast-forward
git merge --no-ff <branch-name>
```

### Rebase
```bash
# Перебазирование текущей ветки
git rebase <branch-name>

# Интерактивное перебазирование
git rebase -i <commit-hash>
```

### Разрешение конфликтов
```bash
# После возникновения конфликта
# 1. Редактируем файлы с конфликтами
# 2. Добавляем разрешенные файлы
git add <resolved-file>
# 3. Продолжаем слияние
git merge --continue
# или
git rebase --continue
```

## Работа с удаленным репозиторием

### Базовые операции
```bash
# Добавление удаленного репозитория
git remote add <name> <url>

# Получение изменений
git fetch <remote>

# Отправка изменений
git push <remote> <branch>

# Получение и слияние изменений
git pull <remote> <branch>
```

### Управление удаленными ветками
```bash
# Отправка новой ветки
git push -u <remote> <branch>

# Удаление удаленной ветки
git push <remote> --delete <branch>
```

## Рабочие процессы

### Git Flow
```bash
# Основные ветки
- master (production)
- develop
- feature/*
- release/*
- hotfix/*

# Создание feature ветки
git checkout -b feature/new-feature develop

# Завершение feature
git checkout develop
git merge --no-ff feature/new-feature
git branch -d feature/new-feature
git push origin develop
```

### Trunk-Based Development
```bash
# Работа с короткоживущими ветками
git checkout -b feature/new-feature
# Разработка
git checkout main
git merge feature/new-feature
git branch -d feature/new-feature
```

## Полезные команды

### Stash
```bash
# Сохранение изменений
git stash

# Просмотр stash
git stash list

# Применение stash
git stash apply
git stash pop

# Удаление stash
git stash drop
```

### .gitignore
```bash
# Пример .gitignore
node_modules/
.env
*.log
.DS_Store
```

### Просмотр изменений
```bash
# Просмотр изменений
git diff

# Просмотр изменений в индексе
git diff --cached

# Просмотр изменений между коммитами
git diff <commit1> <commit2>
```

### Визуализация истории
```bash
# Графическое представление истории
git log --graph --oneline --all

# Просмотр изменений в файле
git log -p <file>
```