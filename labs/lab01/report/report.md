---
## Front matter
title: "Отчёт по лабораторной работе 1"
subtitle: "Подготовка лабораторного стенда"
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

Целью данной работы является приобретение практических навыков установки Rocky Linux на виртуальную машину с помощью инструмента Vagrant.

# Выполнение

## Развёртывание лабораторного стенда на ОС Windows

В конфигурационном файле `Vagrantfile` настроены общие параметры провижининга для всех виртуальных машин.  
В общую секцию включены три shell-скрипта:

- `01-dummy.sh` – заглушка для проверки механизма провижининга;
- `01-hostname.sh` – настройка hostname;
- `01-user.sh` – создание пользовательской учётной записи.

Фрагмент конфигурации с общими провижинерами показан на скриншоте:

![Фрагмент Vagrantfile с общими скриптами провижининга](Screenshot_1.png){ #fig:001 width=80% }

Далее в `Vagrantfile` определяется конфигурация виртуальной машины `server` (секция `config.vm.define "server"`).

В скрипте `01-hostname.sh` задаётся имя пользователя в переменной `username` и выполняется установка полного доменного имени хоста (FQDN) с использованием утилиты `hostnamectl`.  
К базовому имени машины добавляется домен вида `<username>.net`.

![Скрипт настройки hostname](Screenshot_2.png){ #fig:002 width=70% }

В результате на сервере формируется имя хоста вида `server.trseidaliev.net`.

Скрипт `01-user.sh` выполняет:

- вывод сообщения о запуске скрипта;
- определение имени пользователя (`username=trseidaliev`) и пароля;
- генерацию зашифрованного пароля через `openssl`;
- проверку существования учётной записи;
- создание пользователя с добавлением в группу `wheel`, если пользователь ещё не существует;
- определение домашнего каталога и добавление в `.bashrc` настройки приглашения командной строки `PS1`.

![Скрипт создания пользователя и настройки окружения](Screenshot_3.png){ #fig:003 width=80% }

В результате автоматического провижининга на виртуальной машине создаётся пользователь `trseidaliev` с заданным паролем и базовой настройкой оболочки.

Для корректной работы Guest Additions в VirtualBox был установлен плагин `vagrant-vbguest`.  
Установка выполнялась командой `vagrant plugin install ...`, после чего в консоли отобразилось успешное завершение инсталляции плагина `vagrant-vbguest (0.32.0)`.

![Установка плагина vagrant-vbguest](Screenshot_4.png){ #fig:004 width=90% }

Сформированный ранее box-файл Rocky Linux был зарегистрирован в локальном хранилище Vagrant под именем `rockylinux10`.  
В выводе видно успешное добавление box для провайдера `virtualbox (amd64)`.

![Добавление box-файла Rocky Linux в Vagrant](Screenshot_5.png){ #fig:005 width=90% }

Из каталога проекта выполнен запуск виртуальной машины `server` командой `vagrant up server`.  
На экране отображается процесс:

- импорт базового box `rockylinux10`;
- клонирование виртуальной машины;
- настройка сетевых параметров;
- присвоение имени виртуальной машине в VirtualBox;
- информация о синхронизированных каталогах.

![Запуск виртуальной машины server в Vagrant](Screenshot_6.png){ #fig:006 width=90% }

После завершения команды виртуальная машина `server` была успешно поднята и готова к работе.

В графическом окружении Rocky Linux выполнен вход под пользователем `trseidaliev`.  
В приглашении командной строки отображается полное доменное имя хоста `server.trseidaliev.net`, что подтверждает корректную работу скрипта настройки hostname и создания пользователя.

![Терминал под пользователем trseidaliev на сервере](Screenshot_7.png){ #fig:007 width=70% }

Из командной строки Windows в каталоге проекта выполнено подключение к серверу командой `vagrant ssh server`.  
Vagrant запросил пароль пользователя `vagrant`, после чего был открыт SSH-сеанс на машине `server`.

После подключения произведён переход к пользователю `trseidaliev` командой `su`.  
В приглашении командной строки подтверждается успешная смена пользователя и сохранение FQDN `server.trseidaliev.net`.  
После проверки выполнен выход из учётной записи и закрытие SSH-сеанса, о чём свидетельствует сообщение `Connection to 127.0.0.1 closed`.

![SSH-подключение к серверу и смена пользователя](Screenshot_8.png){ #fig:008 width=90% }

# Заключение

В ходе выполнения работы:

- подготовлен Vagrant-проект с общими провижинерами для всех виртуальных машин;
- реализованы скрипты автоматической настройки hostname и создания пользователя `trseidaliev`;
- установлен плагин `vagrant-vbguest` и добавлен box-файл Rocky Linux в Vagrant;
- развернута виртуальная машина `server` в VirtualBox;
- проверен вход в систему как из графического интерфейса, так и по SSH, а также переход к созданному пользователю и корректность настроек окружения.

# Контрольные вопросы

## 1. Для чего предназначен Vagrant?

Vagrant — инструмент для автоматизации создания и управления виртуальными машинами.  
Он обеспечивает быстрое развёртывание идентичных сред, упрощает тестирование, разработку и конфигурацию инфраструктуры.

## 2. Что такое box-файл? В чём назначение Vagrantfile?

**Box-файл** — заранее подготовленный образ виртуальной машины (шаблон ОС), который Vagrant использует для создания ВМ.

**Vagrantfile** — основной конфигурационный файл проекта.  
В нём задаются параметры виртуальных машин: ресурсы, провайдер, сетевые настройки, провижининг, используемые box-файлы и роли ВМ.

## 3. Основные команды Vagrant

- **vagrant init** — создать Vagrantfile.  
- **vagrant up** — создать и запустить виртуальную машину.  
- **vagrant halt** — корректно остановить виртуальную машину.  
- **vagrant destroy** — удалить виртуальную машину.  
- **vagrant ssh** — подключиться по SSH к созданной машине.  
- **vagrant box add/remove/list** — управление box-файлами.  
- **vagrant reload** — перезапустить ВМ с применением новых настроек.  
- **vagrant provision** — повторно выполнить провижининг.

Пример:  
`vagrant up server` — запуск машины *server*.

## 4. Краткие пояснения файлов проекта

### vagrant-rocky.pkr.hcl
Файл Packer.  
Определяет процесс сборки box-файла: базовый ISO Rocky Linux, параметры виртуализации, загрузку kickstart-файла, шаги установки и финальную упаковку образа.

### ks.cfg
Kickstart-файл для автоматической установки ОС.  
Содержит инструкции установщику: разметку диска, сетевые параметры, локаль, пользователей, пароли, пакеты, постустановочные команды.

### Vagrantfile
Конфигурация Vagrant-проекта.  
Определяет виртуальные машины (server, client), настройки CPU/RAM, сетевые параметры, используемый box, а также подключённые скрипты провижининга (настройка hostname, создание пользователя и др.).

### Makefile
Скрипт автоматизации.  
Обычно содержит цели для сборки box, очистки, запуска/остановки VM или обёртки вокруг команд Packer и Vagrant.

