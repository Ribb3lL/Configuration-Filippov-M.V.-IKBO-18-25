# Конфигурационное управление, практика №2
Филиппов М.В., ИКБО-18-25, РТУ-МИРЭА

## Задача 1
Вывести служебную информацию о пакете matplotlib (Python). Разобрать основные элементы содержимого файла со служебной информацией из пакета. Как получить пакет без менеджера пакетов, прямо из репозитория?

Вывод информации об установленном пакете:
```bash
pip show matplotlib
```

Получение пакета напрямую из репозитория:
```bash
git clone https://github.com/matplotlib/matplotlib.git
```

## Задача 2
Вывести служебную информацию о пакете express (JavaScript). Разобрать основные элементы содержимого файла со служебной информацией из пакета. Как получить пакет без менеджера пакетов, прямо из репозитория?

Вывод служебной информации через API реестра npm:
```bash
curl -s https://registry.npmjs.org/express/latest | jq .
```

Получение пакета напрямую из репозитория:
```bash
git clone https://github.com/expressjs/express.git
```

## Задача 3
Сформировать graphviz-код и получить изображения зависимостей matplotlib и express.

Установить graphviz:
```bash
sudo dnf install graphviz
```

Создание файла для matplotlib:
```bash
nano matplotlib.dot
```

Вставка фрагмента кода:
```bash
digraph MatplotlibDeps {
    rankdir=LR;
    node [shape=box, style=rounded, fontname="Helvetica"];

    "matplotlib" -> "contourpy";
    "matplotlib" -> "cycler";
    "matplotlib" -> "fonttools";
    "matplotlib" -> "kiwisolver";
    "matplotlib" -> "numpy";
    "matplotlib" -> "packaging";
    "matplotlib" -> "pillow";
    "matplotlib" -> "pyparsing";
    "matplotlib" -> "python-dateutil";

    "python-dateutil" -> "six";
}
```

Генерация картинки графа:
```bash
dot -Tpng matplotlib.dot -o matplotlib.png
```

Создание файла для express:
```bash
nano express.dot
```

Вставка фрагмента кода:
```bash
digraph ExpressDeps {
    rankdir=LR;
    node [shape=box, style=rounded, fontname="Helvetica"];

    "express" -> "accepts";
    "express" -> "body-parser";
    "express" -> "cookie";
    "express" -> "debug";
    "express" -> "finalhandler";
    "express" -> "parseurl";
    "express" -> "qs";
    "express" -> "send";
    "express" -> "serve-static";

    "body-parser" -> "bytes";
    "send" -> "mime";
}
```

Генерация картинки графа:
```bash
dot -Tpng express.dot -o express.png
```

## Задача 4
Следующие задачи можно решать с помощью инструментов на выбор:

Решатель задачи удовлетворения ограничениям (MiniZinc).
SAT-решатель (MiniSAT).
SMT-решатель (Z3).
Изучить основы программирования в ограничениях. Установить MiniZinc, разобраться с основами его синтаксиса и работы в IDE.

Решить на MiniZinc задачу о счастливых билетах. Добавить ограничение на то, что все цифры билета должны быть различными (подсказка: используйте all_different). Найти минимальное решение для суммы 3 цифр.

Создание файла:
```bash
nano lucky_ticket.mzn
```

Вставка фрагмента кода:
```bash
include "alldifferent.mzn";
array[1..6] of var 0..9: d;
constraint all_different(d);
constraint d[1] + d[2] + d[3] = d[4] + d[5] + d[6];
var int: sum3 = d[1] + d[2] + d[3];
solve minimize sum3;
output [
    "Счастливый билет: ", 
    show(d[1]), show(d[2]), show(d[3]), " ",
    show(d[4]), show(d[5]), show(d[6]), "\n",
    "Минимальная сумма: ", show(sum3), "\n"
];
```

Скачивание официального архива MiniZinc и запуск расчёта:
```bash
curl -L -O https://github.com/MiniZinc/MiniZincIDE/releases/download/2.8.5/MiniZincIDE-2.8.5-bundle-linux-x86_64.tgz
tar -xf MiniZincIDE-2.8.5-bundle-linux-x86_64.tgz
./MiniZincIDE-2.8.5-bundle-linux-x86_64/bin/minizinc lucky_ticket.mzn
```

## Задача 5
различными (подсказка: используйте all_different). Найти минимальное решение для суммы 3 цифр.

Задача 5
Решить на MiniZinc задачу о зависимостях пакетов для рисунка

Создание файла:
```bash
nano task5.mzn
```

Вставка фрагмента кода:
```bash
% 1 -> v1.0.0, 2 -> v2.0.0
var 1..2: A;
var 1..2: B;
var 1..2: C;

% A 1.0.0 -> C 1.0.0; A 2.0.0 -> C 2.0.0
constraint (A == 1) -> (C == 1);
constraint (A == 2) -> (C == 2);

% B 1.0.0 -> C 2.0.0
constraint (B == 1) -> (C == 2);

solve satisfy;

output [
    "Resolved versions:\n",
    "  A = ", if fix(A) == 1 then "1.0.0" else "2.0.0" endif, "\n",
    "  B = ", if fix(B) == 1 then "1.0.0" else "2.0.0" endif, "\n",
    "  C = ", if fix(C) == 1 then "1.0.0" else "2.0.0" endif, "\n"
];
```

Запуск:
```bash
minizinc task5.mzn
```

## Задача 6
Решить на MiniZinc задачу о зависимостях пакетов для следующих данных:

root 1.0.0 зависит от foo ^1.0.0 и target ^2.0.0.
foo 1.1.0 зависит от left ^1.0.0 и right ^1.0.0.
foo 1.0.0 не имеет зависимостей.
left 1.0.0 зависит от shared >=1.0.0.
right 1.0.0 зависит от shared <2.0.0.
shared 2.0.0 не имеет зависимостей.
shared 1.0.0 зависит от target ^1.0.0.
target 2.0.0 и 1.0.0 не имеют зависимостей.

Создание файла:
```bash
nano task6.mzn
```

Вставка фрагмента кода:
```bash
% Кодирование версий: 0 - пакет не установлен

% foo: 1 -> 1.0.0, 2 -> 1.1.0
var 1..2: foo;

% target: 1 -> 1.0.0, 2 -> 2.0.0
var 1..2: target;

% left: 0 -> не установлен, 1 -> 1.0.0
var 0..1: left;

% right: 0 -> не установлен, 1 -> 1.0.0
var 0..1: right;

% shared: 0 -> не установлен, 1 -> 1.0.0, 2 -> 2.0.0
var 0..2: shared;

% Требования root 1.0.0:
% foo ^1.0.0 (любая из версий 1.0.0 или 1.1.0)
constraint foo in {1, 2};
% target ^2.0.0 (строго версия 2.0.0)
constraint target == 2;

% foo 1.1.0 (код 2) требует left ^1.0.0 и right ^1.0.0
constraint (foo == 2) -> (left == 1 /\ right == 1);
% foo 1.0.0 (код 1) не имеет зависимостей
constraint (foo == 1) -> (left == 0 /\ right == 0);

% left 1.0.0 требует shared >= 1.0.0 (версия 1 или 2)
constraint (left == 1) -> (shared >= 1);

% right 1.0.0 требует shared < 2.0.0 (строго версия 1)
constraint (right == 1) -> (shared == 1);

% shared 1.0.0 требует target ^1.0.0 (строго версия 1)
constraint (shared == 1) -> (target == 1);

solve satisfy;

output [
    "Resolved packages:\n",
    "  root:   1.0.0\n",
    "  foo:    ", if fix(foo) == 1 then "1.0.0" else "1.1.0" endif, "\n",
    "  target: ", if fix(target) == 1 then "1.0.0" else "2.0.0" endif, "\n",
    "  left:   ", if fix(left) == 0 then "not installed" else "1.0.0" endif, "\n",
    "  right:  ", if fix(right) == 0 then "not installed" else "1.0.0" endif, "\n",
    "  shared: ", if fix(shared) == 0 then "not installed" else (if fix(shared) == 1 then "1.0.0" else "2.0.0" endif) endif, "\n"
];
```

Запуск:
```bash
minizinc task6.mzn
```

## Задача 7
Представить задачу о зависимостях пакетов в общей форме. Здесь необходимо действовать аналогично реальному менеджеру пакетов. То есть получить описание пакета, а также его зависимости в виде структуры данных. Например, в виде словаря. В предыдущих задачах зависимости были явно заданы в системе ограничений. Теперь же систему ограничений надо построить автоматически, по метаданным.

Создание файла:
```bash
nano task7.py
```

Вставка фрагмента кода:
```bash
from z3 import And, Implies, Int, Or, Solver, sat

repo = {
    'root': {'1.0.0': {'foo': '^1.0.0', 'target': '^2.0.0'}},
    'foo': {'1.0.0': {}, '1.1.0': {'left': '^1.0.0', 'right': '^1.0.0'}},
    'left': {'1.0.0': {'shared': '>=1.0.0'}},
    'right': {'1.0.0': {'shared': '<2.0.0'}},
    'shared': {'1.0.0': {'target': '^1.0.0'}, '2.0.0': {}},
    'target': {'1.0.0': {}, '2.0.0': {}},
}


def semver_ok(v, req):
  v_num = list(map(int, v.split('.')))
  if req.startswith('^'):
    b = list(map(int, req[1:].split('.')))
    return v_num[0] == b[0] and v_num >= b
  if req.startswith('>='):
    return v_num >= list(map(int, req[2:].split('.')))
  if req.startswith('<'):
    return v_num < list(map(int, req[1:].split('.')))
  return v == req


solver = Solver()
v_map = {p: sorted(list(vers.keys())) for p, vers in repo.items()}
x = {p: Int(p) for p in repo}

# Домены версий (0 - не установлен, 1..N - индекс версии)
for p in repo:
  solver.add(x[p] >= 0, x[p] <= len(v_map[p]))

solver.add(x['root'] == v_map['root'].index('1.0.0') + 1)

for p, vers in repo.items():
  for idx, (v, deps) in enumerate(vers.items(), 1):
    clauses = []
    for dp, req in deps.items():
      valid = [i + 1 for i, dv in enumerate(v_map[dp]) if semver_ok(dv, req)]
      clauses.append(Or([x[dp] == vi for vi in valid]) if valid else False)
    if clauses:
      solver.add(Implies(x[p] == idx, And(clauses)))

if solver.check() == sat:
  m = solver.model()
  for p in sorted(repo):
    idx = m[x[p]].as_long()
    if idx > 0:
      print(f'{p}: {v_map[p][idx-1]}')
```

Запуск программы:
```bash
python3 task7.py
```
