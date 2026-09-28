# Конфигурационное управление, практика №1
Филиппов М.В., ИКБО-18-25, РТУ-МИРЭА

## Задача 1
Вывести отсортированный в алфавитном порядке список имен пользователей в файле passwd (вам понадобится grep)
```bash
grep -v '^#' /etc/passwd | cut -d: -f1 | sort
```
## Задача 2
Вывести данные /etc/protocols в отформатированном и отсортированном порядке для 5 наибольших портов
```bash
rep -v '^#' /etc/protocols | awk '{print $2, $1}' | sort -n -r | head -n 5
```

## Задача 3
Написать программу banner средствами bash для вывода текстов
```bash
text="$1"
len=$((${#text} + 2))

dashes=$(printf '%*s' "$len" '' | tr ' ' '-')
border="+${dashes}+"

echo "$border"
echo "| $text |"
echo "$border"
```
## Задача 4
Написать программу для вывода всех идентификаторов (по правилам C/C++ или Java) в файле (без повторений)

Файл get_idnf:
```bash
grep -oE '[a-zA-Z_][a-zA-Z0-9_]*' "$1" | sort -u | tr '\n' ' '
echo
```
Файл hello.c:
```bash
#include <stdio.h>
int main(void) {
        print("hello world!\n");
        return 0;
}
```

## Задача 5
Написать программу для регистрации пользовательской команды (правильные права доступа и копирование в /usr/local/bin)
```bash
chmod 755 "$1"
sudo cp "$1" /usr/local/bin/
echo "Команда '$1' успешно зарегестрирована в /usr/local/bin"
```
