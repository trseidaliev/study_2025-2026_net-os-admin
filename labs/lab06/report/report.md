---
## Front matter
title: "Отчёт по лабораторной работе 6"
subtitle: "Установка и настройка системы управления базами данных MariaDB"
author: "Сейдалиев Тагиетдин Ровшенович"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---


# Цель работы

Приобретение практических навыков по установке и конфигурированию системы управления базами данных на примере программного обеспечения MariaDB.

# Выполнение

## Установка MariaDB

После установки пакетов `mariadb` и `mariadb-server` были просмотрены конфигурационные файлы в каталоге `/etc/my.cnf.d`. Содержимое каталога показано на рисунке:

![Содержимое каталога /etc/my.cnf.d](Screenshot_1.png){ #fig:001 width=80% }

Также был изучен основной конфигурационный файл `/etc/my.cnf`:

Построчно разберём содержание файла `/etc/my.cnf`:

- `#` — строки-комментарии, поясняющие структуру файла.
- Блок `[client-server]` задаёт общие параметры, применяемые одновременно к клиенту `mysql` и серверу `mysqld`.
- Директива `!includedir /etc/my.cnf.d` подключает все дополнительные конфигурационные файлы из соответствующего каталога.

Служба MariaDB была успешно запущена и добавлена в автозагрузку. Проверка прослушивания порта показала, что процесс `mariadbd` принимает соединения на стандартном порту 3306:

![Запуск и проверка прослушивания порта](Screenshot_2.png){ #fig:002 width=90% }

Для обеспечения базовой безопасности был запущен скрипт интерактивной настройки `mysql_secure_installation`. В ходе работы были выполнены следующие действия:

- установлен пароль для пользователя `root` MariaDB;
- отключён удалённый доступ пользователя root;
- удалены анонимные пользователи;
- удалена тестовая база данных `test`;
- перезагружены таблицы привилегий.

Процесс работы утилиты приведён на скриншоте:

![Процесс mysql_secure_installation](Screenshot_3.png){ #fig:003 width=90% }

После входа в MariaDB был отображён список доступных клиентских команд:

![Список клиентских команд mysql](Screenshot_4.png){ #fig:004 width=90% }

Для анализа доступных баз данных использована команда `SHOW DATABASES`, вывод которой показан ниже:

![Результат SHOW DATABASES](Screenshot_5.png){ #fig:005 width=45% }

В системе присутствуют следующие стандартные базы:

- `information_schema` — метаданные объектов базы данных;
- `mysql` — система управления пользователями и правами;
- `performance_schema` — данные о работе сервера;
- `sys` — представления для удобного анализа производительности.

## Конфигурация кодировки символов UTF-8

Команда `status` отобразила текущие параметры сервера. Все значения кодировок (`Server`, `Db`, `Client`) указывали использование `latin1`:

![Статус MariaDB до изменения кодировки](Screenshot_6.png){ #fig:006 width=90% }

В каталоге `/etc/my.cnf.d` был создан файл `utf8.cnf` следующего содержания:

![Файл utf8.cnf](Screenshot_7.png){ #fig:007 width=60% }

Файл включает параметры:

- для клиента: `default-character-set = utf8`;
- для сервера: `character-set-server = utf8`.

Это гарантирует использование UTF-8 на всех уровнях взаимодействия.

После перезапуска MariaDB повторный вызов команды `status` показал изменение всех кодировок на `utf8mb3`, что подтверждает успешное применение настроек:

![Статус MariaDB после применения кодировки UTF-8](Screenshot_8.png){ #fig:008 width=90% }

Изменились следующие параметры:

- Server characterset;
- Db characterset;
- Client characterset;
- Connection characterset.

Все они теперь используют UTF-8, что обеспечивает корректную работу с многоязычными данными.

## Создание базы данных и таблиц

После входа в окружение MariaDB с административными правами была создана база данных `addressbook` с кодировкой UTF-8. На рисунке показан процесс её создания, выбор активной базы и вывод списка таблиц:

Так как база только что создана, она не содержит таблиц, что подтверждается выводом `SHOW TABLES`.

Далее создана таблица `city` с двумя текстовыми полями: `name` и `city`. После создания таблица была заполнена тремя строками со сведениями о пользователях:

![Создание таблицы и заполнение данными](Screenshot_9.png){ #fig:009 width=80% }

Запрос `SELECT * FROM city;` вывел все записи таблицы:

- Иванов — Москва  
- Петров — Сочи  
- Сидоров — Дубна

Это подтверждает корректное создание структуры таблицы и успешное добавление данных.

Для работы с базой был создан отдельный пользователь. На рисунке показано создание пользователя, выдача ему прав на базу и обновление привилегий:

![Создание пользователя и выдача прав](Screenshot_10.png){ #fig:010 width=80% }

Права `SELECT, INSERT, UPDATE, DELETE` позволяют пользователю выполнять базовые операции с данными.

Команда `DESCRIBE city;` отобразила структуру таблицы:

- поле `name` — тип `varchar(40)`  
- поле `city` — тип `varchar(40)`  

Это подтверждает корректность схемы данных.

Проверен общий список баз данных:

![Просмотр баз данных](Screenshot_11.png){ #fig:011 width=60% }

В списке присутствуют:

- addressbook  
- information_schema  
- mysql  
- performance_schema  
- sys  

Также были просмотрены таблицы в базе addressbook как от имени root, так и от имени созданного пользователя. Оба варианта показывают таблицу `city`, что подтверждает корректность прав доступа.

## Резервное копирование и восстановление

В каталоге `/var/backup` были созданы резервные копии базы данных:

- обычная SQL-копия;
- сжатая gzip-копия;
- сжатая копия с указанием текущей даты и времени в имени файла.

Результаты выполнения представлены на скриншоте:

![Создание и восстановление резервных копий](Screenshot_12.png){ #fig:012 width=90% }

Созданные файлы:

- `addressbook.sql`  
- `addressbook.sql.gz`  
- `addressbook.YYYYMMDD.HHMMSS.sql.gz`

Также выполнено восстановление базы из обычной и сжатой копии. Обе операции завершились успешно.

## Подготовка окружения для автоматизации

Для последующего использования в Vagrant конфигурации создан каталог структуры `/vagrant/provision/server/mysql`, в который были скопированы:

- конфигурационные файлы MariaDB;
- резервные копии базы addressbook.

Следом создан исполняемый скрипт `mysql.sh`, выполняющий автоматическую установку и конфигурирование MariaDB. Его фрагмент приведён на рисунке:

![Скрипт автоматизации mysql.sh](Screenshot_13.png){ #fig:013 width=90% }

# Заключение

В процессе работы:

- создана база данных `addressbook` и таблица `city`;
- добавлены записи с данными о трёх пользователях;
- создан пользователь с правами на операции в базе `addressbook`;
- проверены структура таблицы и доступность базы через `mysqlshow`;
- выполнено резервное копирование базы в обычном и сжатом формате, а также восстановление данных;
- подготовлена структура каталогов и скрипт `mysql.sh` для автоматизации установки и конфигурации MariaDB.

# Контрольные вопросы

## 1. Какая команда отвечает за настройки безопасности в MariaDB?

За базовую настройку безопасности отвечает команда:

`mysql_secure_installation`

Она позволяет задать пароль root, удалить тестовые базы, отключить анонимных пользователей и запретить удалённый вход root.

## 2. Как настроить MariaDB для доступа через сеть?

Для сетевого доступа требуется:

* в файле конфигурации (`/etc/my.cnf` или `/etc/my.cnf.d/*.cnf`) задать параметр
  `bind-address = 0.0.0.0` или указать нужный IP;
* обеспечить открытие порта 3306 в firewall;
* создать пользователя с доступом по сети, например:
  `CREATE USER user@'%' IDENTIFIED BY 'password';`

## 3. Какая команда позволяет получить обзор доступных баз данных?

`SHOW DATABASES;`

## 4. Какая команда позволяет узнать, какие таблицы доступны в базе данных?

`SHOW TABLES;`

## 5. Какая команда позволяет узнать, какие поля доступны в таблице?

`DESCRIBE table_name;`
или
`SHOW COLUMNS FROM table_name;`

## 6. Какая команда позволяет узнать, какие записи доступны в таблице?

`SELECT * FROM table_name;`

## 7. Как удалить запись из таблицы?

Используется команда:

`DELETE FROM table_name WHERE условие;`

Например:

`DELETE FROM city WHERE name='Иванов';`

## 8. Где расположены файлы конфигурации MariaDB? Что можно настроить?

Конфигурационные файлы находятся в:

* `/etc/my.cnf`
* `/etc/my.cnf.d/`

С помощью конфигурации можно задавать:

* сетевые настройки;
* кодировки и локаль;
* параметры хранения данных;
* поведение сервера (кэш, буферы, ограничения);
* пути к файлам журналов и сокетам.

## 9. Где располагаются файлы с базами данных MariaDB?

Все базы данных находятся в каталоге:

`/var/lib/mysql/`

В нём для каждой базы создаётся отдельная директория.

## 10. Как сделать резервную копию базы данных и затем её восстановить?

**Резервная копия:**

* в SQL-файл:
  `mysqldump -u root -p database > backup.sql`
* в сжатом виде:
  `mysqldump -u root -p database | gzip > backup.sql.gz`

**Восстановление:**

* из обычного файла:
  `mysql -u root -p database < backup.sql`
* из сжатого файла:
  `zcat backup.sql.gz | mysql -u root -p database`
