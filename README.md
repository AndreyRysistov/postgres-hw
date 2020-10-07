# Домашняя работа по дисциплине "Проектирование и сопровождение баз данных"

## Ссылки

- [Домашняя работа 1](#домашняя-работа-1)
- [Домашняя работа 2](#домашняя-работа-2)
- [Домашняя работа 3](#домашняя-работа-3)
- [Домашняя работа 4](#домашняя-работа-4)
- [Домашняя работа 5](#домашняя-работа-5)
- [Домашняя работа 6](#домашняя-работа-6)
- [Домашняя работа 7](#домашняя-работа-7)
- [Домашняя работа 8](#домашняя-работа-8)
- [Домашняя работа 9](#домашняя-работа-9)
- [Домашняя работа 10](#домашняя-работа-10)
- [Домашняя работа 11](#домашняя-работа-11)
- [Финальная работа 1](#финальная-работа-1)
- [Финальная работа 2](#финальная-работа-2)
- [Финальная работа 3](#финальная-работа-3)
- [Финальная работа 4](#финальная-работа-4)

## Домашняя работа 1

<details>
<summary>Сама работа</summary>

---

### Задание

Подумать над выбором предметной области для выполнения финальной (экзаменационной) работы.
Выбирайте предметную область, которая вам интересна
и в которой вы разбираетесь или хотите разобраться.

Сделать краткое описание выбранной предметной области (1-2 страницы).
Если описание получится более объемным, не беда.
Ведь это описание затем войдет в финальный отчет.

Попытаться сформулировать требования к будущей базе данных.

### Работа

#### Цель работы

Описать словесно выбранную предметную область – "Расписание кафедры".

#### Описание предметной области

В настоящее время весь документооборот перемещается в электронный вид.
Требуется хранить различные документы, данные и поддерживать их связность.
Например, необходимо хранить учебное расписание,
обеспечить её связность с учебными группами,
преподавателями и аудиториями,
чтобы можно было в любой момент создать расписание учебных групп, преподавателей и аудиторий.
Это и является основной целью и задачей выбранной предметной области.

Можно хранить расписание в слабо структурированном виде в формате JSON,
но тогда не получится просто извлекать расписание конкретной группы, преподавателя или аудитории,
придётся для этого писать дополнительный ненужный код.
Такую задачу можно положить на реляционную базу данных при правильном проектирований связей таблиц.

При посещении веб-страницы расписания группы пользователь выбирает номер группы и номер недели.
База данных должна, соответственно, предоставить набор предметов на выбранную неделю по выбранной группе,
включить информацию о месте проведения занятий, ФИО преподавателя, который будет вести их.

При посещении веб-страницы расписания преподавателя
пользователь выбирает ФИО преподавателя и номер недели.
База данных должна, соответственно предоставить набор предметов на выбранную неделю по выбранному ФИО,
включить информацию о месте проведения занятий, номер групп, для которых занятие проводится.

[Наверх](#ссылки)

---

</details>

## Домашняя работа 2

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 3 (упражнения 1-4)

### Работа

#### Упражнение 1

Выполним запрос:

```sql
demo=# insert into aircrafts values ('SU9', 'Sukhoi SUperJet-100', 300);
ERROR:  0A000: cannot insert into column "model" of view "aircrafts"
DETAIL:  View columns that are not columns of their base relation are not updatable.
```

Ошибка нам говорит о том, что
колонки, не являющимися базовыми колонками своего отношения не могут обновляться через представление.

Посмотрим, какие колонки не являются базовыми для отношения:

```sql
demo=# \d+ aircrafts
                                       View "bookings.aircrafts"
    Column     |     Type     | Collation | Nullable | Default | Storage  |         Description
---------------+--------------+-----------+----------+---------+----------+-----------------------------
 aircraft_code | character(3) |           |          |         | extended | Aircraft code, IATA
 model         | text         |           |          |         | extended | Aircraft model
 range         | integer      |           |          |         | plain    | Maximal flying distance, km
View definition:
 SELECT ml.aircraft_code,
    ml.model ->> lang() AS model,
    ml.range
   FROM aircrafts_data ml;
```

Такой колонкой является `model`, соответственно.

#### Упражнение 2

Пример запроса:

```sql
demo=# select * from aircrafts order by range desc;
 aircraft_code |        model        | range
---------------+---------------------+-------
 773           | Боинг 777-300       | 11100
 763           | Боинг 767-300       |  7900
 319           | Аэробус A319-100    |  6700
 320           | Аэробус A320-200    |  5700
 321           | Аэробус A321-200    |  5600
 733           | Боинг 737-300       |  4200
 SU9           | Сухой Суперджет-100 |  3000
 CR2           | Бомбардье CRJ-200   |  2700
 CN1           | Сессна 208 Караван  |  1200
(9 rows)
```

#### Упражнение 3

Пример такого запроса:

```sql
demo=# update aircrafts set range = range * 2 where model ~ 'Сухой Суперджет*';
UPDATE 1
```

#### Упражнение 4

Пример такого запроса:

```sql
demo=# delete from aircrafts where model = 'ЭТО НЕ САМОЛЁТ';
DELETE 0
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 3

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 4 (упражнения 2, 4, 8, 12, 15, 21, 30, 33, 35)

### Работа

#### Упражнение 2

Создадим таблицу, наполним её данными и сделаем выборку по ней.

```sql
demo=# create table test_numeric (measurement numeric, description text);
CREATE TABLE

demo=# insert into test_numeric values
demo-# (1234567890.0987654321, 'Точность 20 знаков, масштаб 10 знаков'),
demo-# (1.5, 'Точность 2 знака, масштаб 1 знак'),
demo-# (0.12345678901234567890, 'Точность 21 знак, масштаб 20 знаков'),
demo-# (1234567890, 'Точность 10 знаков, масштаб 0 знаков (целое число)');
INSERT 0 4

demo=# select * from test_numeric;
      measurement       |                    description
------------------------+----------------------------------------------------
  1234567890.0987654321 | Точность 20 знаков, масштаб 10 знаков
                    1.5 | Точность 2 знака, масштаб 1 знак
 0.12345678901234567890 | Точность 21 знак, масштаб 20 знаков
             1234567890 | Точность 10 знаков, масштаб 0 знаков (целое число)
(4 rows)
```

Да, точность сохранена именно в том виде, в который мы записывали изначально.

#### Упражнение 4

Выполним интересные эксперименты с плавающей точкой:

```sql
demo=# select 2e-45::real > 1e-45::real;
 ?column?
----------
 f
(1 row)

demo=# select 4e-324::double precision > 3e-324::double precision;
 ?column?
----------
 f
(1 row)
```

#### Упражнение 8

```sql
demo=# create table test_serial (id serial primary key, name text);
CREATE TABLE

demo=# INSERT INTO test_serial (name) VALUES ('Вишневая');
INSERT 0 1
```

При выполнении этой команды СУБД выдаст сообщение об ошибке. Почему?

```sql
demo=# INSERT INTO test_serial (id, name) VALUES (2, 'Прохладная');
INSERT 0 1
```

В версии СУБД PostgreSQL 12.4 не выдаёт ошибку.

```sql
demo=# INSERT INTO test_serial (name) VALUES ('Грушевая');
ERROR:  23505: duplicate key value violates unique constraint "test_serial_pkey"
DETAIL:  Key (id)=(2) already exists.
demo=# INSERT INTO test_serial (name) VALUES ('Грушевая');
INSERT 0 1
demo=# INSERT INTO test_serial (name) VALUES ('Зеленая');
INSERT 0 1
demo=# DELETE FROM test_serial WHERE id = 4;
DELETE 1
demo=# INSERT INTO test_serial (name) VALUES ('Луговая');
INSERT 0 1
```

Зато выдала ошибку, что такой ключ уже существует.
Но счётчик `test_serial_pkey` всё равно инкрементировался.

Теперь сделаем выборку.

```sql
demo=# SELECT * FROM test_serial;
 id |    name
----+------------
  1 | Вишневая
  2 | Прохладная
  3 | Грушевая
  5 | Луговая
(4 rows)
```

#### Упражнение 12

```sql
demo=# show datestyle;
 DateStyle
-----------
 ISO, DMY
(1 row)

demo=# set datestyle to 'MDY';
SET

demo=# show datestyle;
 DateStyle
-----------
 ISO, MDY
(1 row)

demo=# SELECT '18-05-2016'::date;
ERROR:  22008: date/time field value out of range: "18-05-2016"
LINE 1: SELECT '18-05-2016'::date;
               ^
HINT:  Perhaps you need a different "datestyle" setting.

demo=# show datestyle;
 DateStyle
-----------
 ISO, MDY
(1 row)
```

Смена конфига `DateStyle` не работает в PostgreSQL 12.4. =(

```sql
demo=# SET datestyle TO 'Postgres, DMY';
SET
Time: 0.619 ms
demo=# show datestyle;
   DateStyle
---------------
 Postgres, DMY
(1 row)

demo=# select current_date;
 current_date
--------------
 04-10-2020
(1 row)

demo=# set datestyle to 'SQL, DMY';
SET
demo=# select current_date;
 current_date
--------------
 04/10/2020
(1 row)

demo=# set datestyle to 'German, DMY';
SET
Time: 0.519 ms
demo=# select current_date;
 current_date
--------------
 04.10.2020
(1 row)
```

#### Упражнение 15

Проэкспериментируем с преобразованием даты в строку.

```sql
demo=# SELECT to_char(current_timestamp, 'ddыmm');
 to_char
---------
 04ы10
(1 row)

demo=# SELECT to_char(current_timestamp, 'MM/DD/YYYY');
  to_char
------------
 10/04/2020
(1 row)

demo=# SELECT to_char(current_timestamp, 'DD-MM-YYYY')::date;
  to_char
------------
 2020-10-04
(1 row)
```

#### Упражнение 21

Предположу, что интервал "1 месяц" добавит единицу ко времени месяца.
В первом случае выведет 2016-02-29, а во втором — 2016-03-29.

```sql
demo=# SELECT ('2016-01-31'::date +'1 mon'::interval) AS new_date;
      new_date
---------------------
 2016-02-29 00:00:00
(1 row)

demo=# SELECT ('2016-02-29'::date +'1 mon'::interval) AS new_date;
      new_date
---------------------
 2016-03-29 00:00:00
(1 row)
```

Отлично, я угадал! =)

#### Упражнение 30

Предположения:

```sql
INSERT INTO test_bool VALUES (TRUE, 'yes');
INSERT INTO test_bool VALUES (yes, 'yes');
-- "yes" — невалидный boolean

INSERT INTO test_bool VALUES ('yes', true);
-- "'yes'" — то же самое, но в виде строки.
-- Если в SQL слабая типизация, то ошибка будет как выше.
-- Если сильная — ошибка типа.
-- Ну и true — невалидная строка.

INSERT INTO test_bool VALUES ('yes', TRUE);
-- То же самое, как выше.

INSERT INTO test_bool VALUES ('1', 'true');
-- '1' — вообще строка.
-- Если типизация сильная, ошибка типа.
-- Если типисация слабая, то будет '1' -> 1 -> true, т.е. без ошибки.

INSERT INTO test_bool VALUES (1, 'true');
-- Зависит от силы типизации, см. выше.

INSERT INTO test_bool VALUES ('t', 'true');
-- Типизация, но надо смотреть, является ли t — булевым значением.

INSERT INTO test_bool VALUES ('t', truth);
-- Ошибка со строкой. Надо в кавычки заворачивать.

INSERT INTO test_bool VALUES (true, true);
-- Ошибка со строкой. Надо в кавычки заворачивать.

INSERT INTO test_bool VALUES (1::boolean, 'true');
-- Явное преобразование. Скорее всего, ошибки не будет.

INSERT INTO test_bool VALUES (111::boolean, 'true');
-- Явное преобразование. Скорее всего, ошибка будет,
-- т.к. не понятно, можно ли 111 привести в boolean.
```

Итог:

```sql
demo=# INSERT INTO test_bool VALUES (TRUE, 'yes');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (yes, 'yes');
ERROR:  42703: column "yes" does not exist
LINE 1: INSERT INTO test_bool VALUES (yes, 'yes');
                                      ^

demo=# INSERT INTO test_bool VALUES ('yes', true);
INSERT 0 1

demo=# INSERT INTO test_bool VALUES ('yes', TRUE);
INSERT 0 1

demo=# INSERT INTO test_bool VALUES ('1', 'true');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (1, 'true');
ERROR:  42804: column "a" is of type boolean but expression is of type integer
LINE 1: INSERT INTO test_bool VALUES (1, 'true');
                                      ^
HINT:  You will need to rewrite or cast the expression.

demo=# INSERT INTO test_bool VALUES ('t', 'true');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES ('t', truth);
ERROR:  42703: column "truth" does not exist
LINE 1: INSERT INTO test_bool VALUES ('t', truth);
                                           ^

demo=# INSERT INTO test_bool VALUES (true, true);
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (1::boolean, 'true');
INSERT 0 1

demo=# INSERT INTO test_bool VALUES (111::boolean, 'true');
INSERT 0 1
```

Вывод: типизация слабая, но неявные преобразования идут через строковые значения.
Целые числа надо преобразовывать явно.

#### Упражнение 33

```sql
demo=# CREATE TABLE food(food text[][]);
CREATE TABLE
demo=# insert into food values ('{ { "сосиска", "макароны", "кофе" },{ "котлета", "каша", "кофе" },{ "сосиска", "каша", "кофе" },{ "котлета", "каша", "чай" } }'::text[][]);
demo=# select food[1][1] from food;
  food
---------
 сосиска
(1 row)
```

#### Упражнение 35

```sql
demo=# SELECT '["хоккей", "теннис"]'::jsonb -> 1;
 ?column?
----------
 "теннис"
(1 row)

demo=# SELECT '{"sports": "хоккей"}'::jsonb -> 'sports';
 ?column?
----------
 "хоккей"
(1 row)

demo=# SELECT '["хоккей", "теннис"]'::jsonb ->> 1;
 ?column?
----------
 теннис
(1 row)

demo=# SELECT '{"sports": "хоккей"}'::jsonb ->> 'sports';
 ?column?
----------
 хоккей
(1 row)

demo=# SELECT '{"sports": "хоккей"}'::jsonb #> '{sports}';
 ?column?
----------
 "хоккей"
(1 row)

demo=# SELECT '{"sports": "хоккей"}'::jsonb #>> '{sports}';
 ?column?
----------
 хоккей
(1 row)

demo=# SELECT '{"sports": "хоккей"}'::jsonb ? 'sports';
 ?column?
----------
 t
(1 row)
```

[Наверх](#ссылки)

---

</details>

## Домашняя работа 4

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 5 (упражнения 2, 9, 17, 18)

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 5

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 6 (упражнения 2, 7, 9, 13, 19, 21, 23)

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 6

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 7 (упражнения 1, 2, 4)

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 7

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 8 (упражнения 1, 3)

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 8

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 9 (упражнения 2, 3)

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 9

<details>
<summary>Сама работа</summary>

---

### Задание

Глава 10 (упражнения 3, 6, 8)

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 10

<details>
<summary>Сама работа</summary>

---

### Предисловие

Программирование на стороне сервера.

Выполняется на основе [презентации](https://edu.postgrespro.ru/sqlprimer/sqlprimer-2019-msu-09.pdf)
и главы 4 учебного пособия "Администрирование информационных систем".
Учебную базу данных можно наполнить информацией, функциями и триггерами
с помощью команд, выполняемых в командной строке Debian:

```bash
createdb ais -U postgres
psql -d ais -f adj_list.sql -U postgres
```

Файл `adj_list.sql` находится в составе архива исходных текстов, прилагаемых к учебному пособию.
Они находятся [здесь](http://www.morgunov.org/docs/inf_sys_admin_prg.tgz)

Эти исходные тексты также есть в виртуальной машине ОС Debian
в каталоге `/home/WORK/Databases/Admin_DB/UTF-8`.
В составе этих примеров есть и те,
которые не приведены в тексте пособия,
но они могут быть полезными при выполнении финального задания.

### Задание

Нужно проделать упражнения 12-18 на стр. 86-88 учебного пособия "Администрирование информационных систем".
Почти все эти упражнения являются простыми, ознакомительными, не требующими программирования.

_Прим. В настоящее время я пишу вторую часть учебника по SQL (продвинутую).
Скорее всего, презентацию по программированию на стороне сервера
я дополню более сложными примерами, тогда и задание будет откорректировано._

### Работа

[Наверх](#ссылки)

---

</details>

## Домашняя работа 11

<details>
<summary>Сама работа</summary>

---

### Предисловие

Полнотекстовый поиск.

Задание выполняется на основе презентации 10 "Полнотекстовый поиск"
и главы 12 документации на [Постгрес](https://postgrespro.ru/docs/postgresql/12/textsearch)

### Задание

Придумать и реализовать пример использования полнотекстового поиска,
аналогичный (можно более простой или более сложный) тому примеру с библиотечным каталогом,
который был приведен в презентации.
Можно использовать исходные тексты, приведенные в [презентации](https://edu.postgrespro.ru/sqlprimer/sqlprimer-2019-msu-10.tgz).

### Работа

[Наверх](#ссылки)

---

</details>

## Финальная работа 1

<details>
<summary>Сама работа</summary>

---

### Задание

Спроектировать базу данных для выбранной предметной области.

Самые первые этапы разработки вы выполните в первом задании (см. выше).
А в финальном задании выполняете остальные этапы.

При этом нужно следовать общей методологии проектирования баз данных:
сначала необходимо создать концептуальную модель данных с использованием ER-диаграмм,
затем построить логическую модель, выполнив отображение сущностей и связей
в отношения, в завершение нужно выполнить физическое проектирование, создав
реляционные таблицы в среде целевой СУБД PostgreSQL. Можно использовать
ту нотацию, которая вам больше нравится: П. Чена, "вороньи лапки"
или UML-нотацию, как в лекции и в учебнике Т. Коннолли. Для рисования диаграмм
можно использовать любой бесплатный редактор, умеющий рисовать ER-диаграммы.
Можно нарисовать их в Word'е или аккуратно от руки (и сфотографировать).

Число таблиц должно быть равно 8-10.

Почему дается интервал (8-10)? Потому что число таблиц может быть 8, а не 10,
но при этом могут использоваться более разнообразные типы данных и может быть
больше столбцов в таблицах.

Обязательно должны быть созданы триггеры (и триггерные функции к ним)
и хранимые функции (процедуры) на языке PL/pgSQL.

- Число триггеров должно быть не менее 2.
- Число хранимых функций должно быть не менее 2.

Эти функции и триггеры не обязательно должны быть сложными. Цель -- научиться
их применять с пользой для дела.

### Работа

[Наверх](#ссылки)

---

</details>

## Финальная работа 2

<details>
<summary>Сама работа</summary>

---

### Задание

Ввести небольшое количество записей в таблицы базы данных, чтобы можно было
продемонстрировать типичные запросы к базе данных. В запросах должны быть
использованы:

- подзапросы;
- общие табличные выражения (CTE) (хотя бы одно) и
- оконные функции (Window functions) (хотя бы одна).

### Работа

[Наверх](#ссылки)

---

</details>

## Финальная работа 3

<details>
<summary>Сама работа</summary>

---

### Задание

Создавать приложение (интерфейс пользователя) не требуется.

Конечно, создать его не запрещается, но это не означает, что при отсутствии
интерфейса пользователя оценка будет снижена, а при его наличии повышена.
Мотивом для его создания может быть стремление к совершенству, желание превратить
эту учебную разработку в полноценный продукт и т. д.

В том случае, если приложение не разрабатывалось, нужно заранее подготовить
несколько типичных запросов к базе данных и сохранить их в отдельных текстовых
файлах. Для демонстрации этих запросов их можно вызывать как извне утилиты psql,
так и изнутри нее. Например, для выполнения запроса, содержащегося в файле
`file_with_request.sql`, нужно сделать так:

```bash
psql -d your_database -f file_with_request.sql -U postgres
```

Для выполнения запроса, сохраненного в файле, изнутри утилиты psql, можно
сделать так (конечно, нужно учитывать, в каком каталоге находится этот файл,
возможно, будет удобнее и проще написать полный путь к файлу):

```sql
\i  file_with_request.sql
```

### Работа

[Наверх](#ссылки)

---

</details>

## Финальная работа 4

<details>
<summary>Сама работа</summary>

---

### Задание

Подготовить отчет.

Нужно поместить в него:

- краткое описание предметной области и требования к базе данных
  (т. е. результат выполнения самого первого домашнего задания);
- концептуальную, логическую и физическую схемы базы данных;
- типичные запросы к базе данных, сохраненные в виде отдельных текстовых файлов;
- резервную копию базы данных, созданную с помощью утилиты pg_dump, входящей
  в состав PostgreSQL. С помощью этой копии можно будет быстро восстановить вашу
  базу данных и те данные, которые вы в нее ввели.

Краткое описание предметной области, концептуальную и логическую схемы базы
данных нужно поместить в документ в формате doc или pdf (как вам удобнее).

Физическую схему базы данных нужно представить в отчете в виде текстового
файла, содержащего SQL-команды для создания таблиц, представлений (если они
используются), триггеров и триггерных функций. В этом файле должны быть сделаны
комментарии. Имея такой файл, создать все объекты базы данных можно будет таким
образом:

```bash
psql -d your_database -f file_with_SQL_commands.sql -U postgres
```

В качестве примера такого файла можно посмотреть файл `adj_list.sql`
в каталоге `/home/WORK/Databases/Admin_DB/UTF-8`.

Для каждого отношения (таблицы) необходимо указать номер нормальной формы,
в которой это отношение находится, и кратко (буквально, в два слова) обосновать,
из чего это следует. Если какое-либо отношение не находится хотя бы в 3НФ,
необходимо обосновать, почему принято такое проектное решение.

### Работа

[Наверх](#ссылки)

---

</details>
