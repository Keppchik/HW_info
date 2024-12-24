# Лабораторная работа: Мониторинг системных ресурсов

## Разминка - Отслеживание загрузки процессора и памяти
1. Сначала получим данные о загрузке процессора для этого используем команду ```top```
```bash
top -bn1 | grep "Cpu(s)" | awk '{print "Загрузка процессора: " $2 "%"}'
```
2. Далее используем команду ```free``` для получения данных о памяти
```bash
free -m | awk 'NR==2{printf "Использование памяти: %.2f%%\n", $3*100/$2}'
```
3. Теперь создадим файл ```monitoring.bash``` и запишем в него следующее:
```bash
#!/bin/bash

echo "=== Мониторинг ресурсов ==="
echo "Процессора:"
top -bn1 | grep "Cpu(s)" | awk '{print $2 "%"}'
echo "Памяти:"
free -m | awk 'NR==2{printf " %.2f%%\n", $3*100/$2}'
```
4. Запустите файл при помощи команды ```bash monitoring.bash```

## Основная задача
### Создать bash-скрипт для мониторинга:
- Загрузки процессора.
- Использования памяти.
- Количество использованного дискового пространства.
- Сетевой активности (количество отправленных и полученных байтов).

### Требования:
- Скрипт должен обновлять информацию каждые 5 секунд.
- Данные должны записываться в файл resources_logs.txt.

## Ресурсы
[Источник где можно найти все](https://google.com)

# Решение
Один из вариантов написания bash-скрипта:
```bash
#!/bin/bash

while true; do
    cpu=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 "%"}')
    memory=$(free -m | awk 'NR==2{printf "%.2f%%", $3*100/$2}')
    disk=$(df -h | awk '$NF=="/"{printf "%s/%s (%s)", $3, $2, $5}')
    rx=$(cat /sys/class/net/enp0s3/statistics/rx_bytes)
    tx=$(cat /sys/class/net/enp0s3/statistics/tx_bytes)

    output="=== $(date) ===
CPU: $cpu
Memory: $memory
Disk: $disk
Network Data:
  Received: $rx
  Sent: $tx
"

    echo "$output" > resources_logs.txt
    sleep 5
done
```
