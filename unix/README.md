 # Unix

## Содержание
1. [Основные команды](#основные-команды)
2. [Работа с файлами](#работа-с-файлами)
3. [Процессы](#процессы)
4. [Права доступа](#права-доступа)
5. [Сеть](#сеть)
6. [Скрипты](#скрипты)
7. [Системное администрирование](#системное-администрирование)

## Основные команды

### Навигация
```bash
# Просмотр текущей директории
pwd

# Смена директории
cd <path>
cd ..  # на уровень выше
cd ~   # в домашнюю директорию

# Просмотр содержимого
ls
ls -l   # подробный вывод
ls -a   # включая скрытые файлы
ls -lh  # человекочитаемый размер
```

### Поиск
```bash
# Поиск файлов
find . -name "*.txt"
find / -type f -name "*.conf"
find . -mtime -7  # измененные за последние 7 дней

# Поиск в файлах
grep "pattern" file.txt
grep -r "pattern" directory/
grep -i "pattern" file.txt  # без учета регистра
```

## Работа с файлами

### Создание и редактирование
```bash
# Создание файла
touch file.txt

# Создание директории
mkdir directory

# Копирование
cp source.txt destination.txt
cp -r source_dir destination_dir

# Перемещение/переименование
mv old.txt new.txt
mv file.txt directory/

# Удаление
rm file.txt
rm -r directory
```

### Просмотр содержимого
```bash
# Вывод содержимого
cat file.txt

# Постраничный просмотр
less file.txt

# Вывод начала файла
head -n 10 file.txt

# Вывод конца файла
tail -n 10 file.txt
tail -f file.txt  # слежение за изменениями
```

## Процессы

### Управление процессами
```bash
# Список процессов
ps
ps aux
ps -ef

# Поиск процесса
ps aux | grep nginx

# Убийство процесса
kill <pid>
kill -9 <pid>  # принудительное завершение

# Мониторинг процессов
top
htop
```

### Фоновые процессы
```bash
# Запуск в фоне
command &

# Переключение между процессами
jobs
fg %1
bg %1

# Отсоединение от терминала
nohup command &
```

## Права доступа

### Изменение прав
```bash
# Изменение прав
chmod 755 file.txt
chmod +x script.sh
chmod u=rwx,g=rx,o= file.txt

# Изменение владельца
chown user:group file.txt
chown -R user:group directory/
```

### Специальные права
```bash
# SUID
chmod u+s file

# SGID
chmod g+s directory

# Sticky bit
chmod +t directory
```

## Сеть

### Основные команды
```bash
# Проверка соединения
ping example.com

# Просмотр сетевых интерфейсов
ifconfig
ip addr

# Маршрутизация
route
ip route

# Сетевые соединения
netstat -tulpn
ss -tulpn
```

### Диагностика
```bash
# Трассировка маршрута
traceroute example.com

# DNS запросы
nslookup example.com
dig example.com

# Сканирование портов
nmap example.com
```

## Скрипты

### Bash скрипты
```bash
#!/bin/bash

# Переменные
name="John"
echo "Hello, $name!"

# Условные операторы
if [ $age -gt 18 ]; then
    echo "Adult"
else
    echo "Minor"
fi

# Циклы
for i in {1..5}; do
    echo $i
done

while [ $count -lt 10 ]; do
    echo $count
    ((count++))
done
```

### Полезные конструкции
```bash
# Проверка существования файла
if [ -f file.txt ]; then
    echo "File exists"
fi

# Проверка директории
if [ -d directory ]; then
    echo "Directory exists"
fi

# Проверка прав
if [ -r file.txt ]; then
    echo "File is readable"
fi
```

## Системное администрирование

### Мониторинг системы
```bash
# Загрузка системы
uptime
w

# Дисковое пространство
df -h
du -sh *

# Память
free -h
vmstat
```

### Планировщик задач
```bash
# Просмотр задач
crontab -l

# Редактирование задач
crontab -e

# Примеры cron
* * * * * command  # каждую минуту
0 * * * * command  # каждый час
0 0 * * * command  # каждый день
0 0 * * 0 command  # каждое воскресенье
```

### Логи
```bash
# Системные логи
tail -f /var/log/syslog
journalctl -f

# Логи приложений
tail -f /var/log/nginx/error.log
tail -f /var/log/apache2/access.log
```