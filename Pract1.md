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

Файл banner:
```bash
text="$1"
len=$((${#text} + 2))

dashes=$(printf '%*s' "$len" '' | tr ' ' '-')
border="+${dashes}+"

echo "$border"
echo "| $text |"
echo "$border"
```

Запуск программы:
```bash
./banner "Hello from RTU MIREA!"
```

## Задача 4
Написать программу для вывода всех идентификаторов (по правилам C/C++ или Java) в файле (без повторений)

Файл get_idnf:
```bash
grep -oE '[a-zA-Z_][a-zA-Z0-9_]*' "$1" | sort -u | tr '\n' ' '
echo
```

Выдача прав файлу:
```bash
chmod +x get_idnf
```

Файл hello.c:
```bash
#include <stdio.h>
int main(void) {
        print("hello world!\n");
        return 0;
}
```

Запуск программы:
```bash
./get_idnf hello.c
```

## Задача 5
Написать программу для регистрации пользовательской команды (правильные права доступа и копирование в /usr/local/bin)
```bash
sudo cp "$1" /usr/local/bin/
echo "Команда '$1' успешно зарегестрирована в /usr/local/bin"
```

Выдача прав файлу:
```bash
chmod +x reg
```

Запуск программы:
```bash
./reg banner
```

## Задача 6
Написать программу для проверки наличия комментария в первой строке файлов с расширением c, js и py.

Файл check_comm:
```bash
for f in *.c *.js *.py; do
        [ -f  "$f" ] || continue

        if head -n 1 "$f" | grep -qE '^([[:space:]]*//|[[:space:]]*/\*|[[:space:]]*#)'; then
                echo "$f: есть комментарий"
        else
                echo "$f: комментария нет"
        fi
done
```

Выдача прав файлу:
```bash
chmod +x check_comm
```

Создание тестовых файлов:
```bash
printf "// комментарий на Си\nint x = 1;\n" > test_ok.c
printf "x = 1\n" > test_no.py
printf "x = 1\n" > test_no.py
```

Запуск программы:
```bash
./check_comm
```

## Задача 7
Написать программу для нахождения файлов-дубликатов (имеющих 1 или более копий содержимого) по заданному пути (и подкаталогам)

Файл find_duplicates:
```bash
find "$1" -type f -exec md5sum {} + | sort | uniq -w32 -d
```

Выдача прав файлу:
```bash
chmod +x find_duplicates
```

Запуск программы:
```bash
find_duplicates test_dup
```

## Задача 8
Написать программу, которая находит все файлы в данном каталоге с расширением, указанным в качестве аргумента и архивирует все эти файлы в архив tar

Файл tar_by:
```bash
if [ $# -lt 2 ]; then
    echo "Использование: $0 <каталог> <расширение>"
    echo "Пример: $0 . txt"
    exit 1
fi

dir="$1"
ext="${2#.}"

find "$dir" -type f -name "*.$ext" -print0 | tar -cvf archive.tar --null -T -
```

Выдача прав файлу:
```bash
chmod +x tar_by
```

Создание тестовых файлов:
```bash
mkdir -p test_tar
echo "файл 1" > test_tar/a.txt
echo "файл 2" > test_tar/b.txt
echo "не архивировать" > test_tar/ignore.c
```

Запуск программы:
```bash
./tar_by_ext test_tar txt
```

Проверка содержания архива:
```bash
tar -tvf archive.tar
```

## Задача 9
Написать программу, которая заменяет в файле последовательности из 4 пробелов на символ табуляции. Входной и выходной файлы задаются аргументами

Файл spaces_to_tabs:
```bash
if [ $# -lt 2 ]; then
    echo "Использование: $0 <входной_файл> <выходной_файл>"
    exit 1
fi
sed 's/    /\t/g' "$1" > "$2"
```

Выдача прав файлу:
```bash
chmod +x spaces_to_tabs
```

Создание файла:
```bash
printf "    строка с 4 пробелами в начале\n" > input.txt
```
Запуск программы:
```bash
./spaces_to_tabs input.txt output.txt
```

Проверка результатов:
```bash
cat -A output.txt
```

## Задача 10
Написать программу, которая выводит названия всех пустых текстовых файлов в указанной директории. Директория передается в программу параметром

Файл empty_txt:
```bash
dir="${1:-.}"

if [ ! -d "$dir" ]; then
    echo "Ошибка: директория '$dir' не найдена"
    exit 1
fi

find "$dir" -maxdepth 1 -type f -empty | while IFS= read -r f; do
    if file "$f" | grep -q "empty"; then
        echo "$f"
    fi
done
```

Выдача прав файлу:
```bash
chmod +x empty_txt
```

Создание тестовой папки и набора файлов:
```bash
mkdir -p test_empty
touch test_empty/empty_a.txt
touch test_empty/empty_b.c
echo "blablabla" > test_empty/full.txt
```

Запуск программы:
```bash
./empty_txt test_empty
```
