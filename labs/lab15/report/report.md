---
## Front matter
title: "Отчёт по лабораторной работе 15"
subtitle: "Настройка сетевого журналирования"
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

Получение навыков по работе с журналами системных событий.

# Выполнение

## Настройка сервера сетевого журнала

В каталоге `/etc/rsyslog.d` был создан конфигурационный файл `netlog-server.conf`, содержащий строки для загрузки модуля `imtcp` и запуска TCP-сервера на порту 514. Это позволяет серверу принимать входящие сообщения журналов от клиентов по TCP.

![Файл netlog-server.conf с включённым приёмом сообщений по TCP 514](Screenshot_1.png){ #fig:003 width=80% }

После внесения настроек служба `rsyslog` была перезапущена, а затем просмотрены открытые TCP-порты, связанные с `rsyslogd`. В выводе отображены прослушиваемые порты и работающие процессы. Также через межсетевой экран был открыт порт 514/tcp, временно и на постоянной основе.

![Вывод lsof с TCP-портами rsyslog и команды настройки firewall-cmd](Screenshot_2.png){ #fig:004 width=90% }

## Настройка клиента сетевого журнала

На клиенте был создан конфигурационный файл `netlog-client.conf`, в котором включена пересылка всех сообщений журнала на сервер по TCP-порту 514. Для перенаправления используется запись `*.* @@server.trseidaliev.net:514`.

![Файл netlog-client.conf с перенаправлением логов на сервер по TCP 514](Screenshot_3.png){ #fig:005 width=80% }

Служба `rsyslog` была перезапущена для применения настроек.

## Просмотр журнала

Для проверки корректности работы логирования на сервере просматривался файл `/var/log/messages` в реальном времени. В выводе присутствуют как локальные сообщения сервера, так и записи, пришедшие от клиента, что указывает на успешную передачу данных.

![Журнал /var/log/messages с сообщениями от client и server](Screenshot_4.png){ #fig:006 width=90% }

На сервере под пользователем `trseidaliev` был запущен графический просмотрщик системных ресурсов и процессов `gnome-system-monitor`, что позволяет анализировать состояние системы и активность процессов.

![Запущенная графическая утилита gnome-system-monitor под пользователем trseidaliev](Screenshot_5.png){ #fig:007 width=70% }

Также предпринималась попытка установки просмотрщика логов `lnav`. Однако пакет отсутствует в репозиториях, о чём сообщает вывод менеджера пакетов.

![Попытка установки lnav с ошибкой «No match for argument»](Screenshot_6.png){ #fig:008 width=90% }

## Внесение изменений в настройки внутреннего окружения виртуальных машин


В каталоге `/vagrant/provision/server/` была создана структура `netlog/etc/rsyslog.d`, в которую помещён файл конфигурации `netlog-server.conf`. Далее был создан файл `netlog.sh`, выполняющий копирование файлов, восстановление контекстов SELinux, настройку firewall и перезапуск `rsyslog`.

![Скрипт провижининга netlog.sh для серверной ВМ](Screenshot_7.png){ #fig:009 width=80% }

В каталоге `/vagrant/provision/client/` была создана аналогичная структура `netlog/etc/rsyslog.d`, куда был помещён файл `netlog-client.conf`. Затем был создан файл `netlog.sh`, выполняющий установку необходимых пакетов, копирование конфигураций и перезапуск службы `rsyslog`.

![Скрипт провижининга netlog.sh для клиентской ВМ](Screenshot_8.png){ #fig:010 width=80% }

# Заключение

В ходе выполнения работы:

- настроен сервер сетевого журналирования с использованием rsyslog и включён приём сообщений по TCP-порту 514;
- создан файл `netlog-server.conf`, активирующий модуль imtcp и работу TCP-сервера журнала;
- проверена работа службы rsyslog и прослушиваемые ею порты, а также открыты необходимые правила в локальном межсетевом экране;
- на клиентской машине создан и настроен файл `netlog-client.conf` для пересылки всех сообщений журнала на сервер;
- выполнена проверка доставки сообщений — в системном журнале сервера отображаются записи как локального хоста, так и клиентской машины;
- произведён просмотр логов с использованием `tail`, `gnome-system-monitor` и попытка установки инструмента lnav;
- подготовлены каталоги, конфигурационные файлы и скрипты провижининга Vagrant для автоматизации настройки сетевого журналирования на сервере и клиенте;
- обеспечено автоматическое копирование конфигураций, настройка SELinux-контекстов, открытие порта 514 и перезапуск rsyslog при развёртывании виртуальных машин.



# Контрольные вопросы

## 1. Какой модуль rsyslog вы должны использовать для приёма сообщений от journald?

Для приёма сообщений от `journald` используется модуль **imjournal**.  
Он обеспечивает интеграцию rsyslog с системным журналом systemd.

## 2. Как называется устаревший модуль, который можно использовать для включения приёма сообщений журнала в rsyslog?

Устаревшим модулем является **imuxsock**.  
Он использовался для чтения сокета `/dev/log`, но в современных системах заменён `imjournal`.

## 3. Чтобы убедиться, что устаревший метод приёма сообщений из journald в rsyslog не используется, какой дополнительный параметр следует использовать?

Следует использовать параметр:

```
SystemLogSocketName=""
```

Его добавляют в конфигурацию journald, чтобы исключить создание сокета `/run/systemd/journal/syslog` и предотвратить передачу сообщений через старый механизм.

## 4. В каком конфигурационном файле содержатся настройки, которые позволяют вам настраивать работу журнала?

Настройки journald находятся в файле:

```
/etc/systemd/journald.conf
```

## 5. Каким параметром управляется пересылка сообщений из journald в rsyslog?

За пересылку отвечает параметр:

```
ForwardToSyslog=
```

Если значение `yes`, journald отправляет сообщения в rsyslog.

## 6. Какой модуль rsyslog вы можете использовать для включения сообщений из файла журнала, не созданного rsyslog?

Для этого применяется модуль **imfile**.  
Он позволяет rsyslog отслеживать любые текстовые файлы и импортировать их содержимое в систему журналирования.

## 7. Какой модуль rsyslog вам нужно использовать для пересылки сообщений в базу данных MariaDB?

Для работы с MariaDB используется модуль **ommysql**.  
Он позволяет rsyslog записывать сообщения в таблицы SQL-базы.

## 8. Какие две строки вам нужно включить в rsyslog.conf, чтобы позволить текущему журнальному серверу получать сообщения через TCP?

Необходимы строки:

```
$ModLoad imtcp
$InputTCPServerRun 514
```

Они включают TCP-модуль и запускают сервер на порту 514.

## 9. Как настроить локальный брандмауэр, чтобы разрешить приём сообщений журнала через порт TCP 514?

Следует открыть порт 514/tcp:

```
firewall-cmd --add-port=514/tcp
firewall-cmd --add-port=514/tcp --permanent
```

После этого необходимо выполнить перезагрузку правил:

```
firewall-cmd --reload
```
