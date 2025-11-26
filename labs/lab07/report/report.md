---
## Front matter
title: "Отчёт по лабораторной работе 7"
subtitle: "Расширенные настройки межсетевого экрана"
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

Получить навыки настройки межсетевого экрана в Linux в части переадресации портов и настройки Masquerading.

# Выполнение

## Создание пользовательской службы firewalld

На сервере был создан модифицированный файл описания службы на основе стандартного ssh.xml.  
После копирования файл был открыт и просмотрен.

![Просмотр оригинального файла службы ssh-custom.xml](Screenshot_1.png){ #fig:001 width=90% }

В файле использован синтаксис XML:

- строка объявления указывает версию и кодировку документа;
- корневой элемент `<service>` описывает параметры службы FirewallD;
- тег `<short>` содержит краткое имя службы;
- элемент `<description>` даёт развёрнутое описание назначения службы;
- элемент `<port>` определяет номер порта и протокол.

В открытом файле изменён порт на значение 2022.  
Описание службы скорректировано для отражения факта модификации.

![Редактирование файла службы с портом 2022](Screenshot_2.png){ #fig:002 width=70% }

Просмотр списка стандартных служб показал отсутствие ssh-custom.  
После перезагрузки правил FirewallD новая служба стала отображаться среди доступных, но не была активирована.

![Получение списка служб и добавление новой службы](Screenshot_3.png){ #fig:003 width=90% }

Новая служба была добавлена в активные службы FirewallD.  
После сохранения изменений и перезагрузки правил ssh-custom стала доступна постоянно.

## Перенаправление портов

На сервере настроено перенаправление трафика с порта 2022 на стандартный порт 22.  
Результат применения правила показан на скриншоте.

![Добавление перенаправления порта через FirewallD](Screenshot_4.png){ #fig:004 width=80% }

На клиентской машине выполнено подключение по SSH через порт 2022.  
Вход прошёл успешно.

![Подключение по SSH через порт 2022](Screenshot_5.png){ #fig:005 width=80% }

## Настройка Port Forwarding и Masquerading

На сервере был выполнен просмотр параметров, отвечающих за перенаправление пакетов.  
По умолчанию значение net.ipv4.ip_forward оказалось равным нулю.

![Проверка параметров ядра на сервере](Screenshot_6.png){ #fig:006 width=90% }

Создан файл в каталоге sysctl.d, содержащий параметр включения пересылки IPv4-пакетов.  
После применения конфигурации forwarding был активирован.

В зоне public активирован masquerading.  
После перезагрузки правил изменения вступили в силу.

На клиенте выполнена проверка — веб-страницы успешно открываются.

![Проверка интернет-доступа с клиента](Screenshot_7.png){ #fig:007 width=90% }

## Внесение изменений в настройки внутреннего окружения виртуальной машины

В каталоге /vagrant/provision/server создана структура подкаталогов для хранения конфигурационных файлов FirewallD и параметров sysctl.  
В соответствующие директории были помещены подготовленные файлы ssh-custom.xml и 90-forward.conf.

В каталоге /vagrant/provision/server создан исполняемый сценарий firewall.sh, предназначенный для развёртывания сетевых настроек при provisioning.  
Содержимое файла приведено на скриншоте.

![Содержимое скрипта firewall.sh](Screenshot_8.png){ #fig:008 width=70% }


# Заключение

В ходе работы:

- создана пользовательская служба firewalld на основе ssh.xml;
- изменён стандартный порт SSH на 2022 и добавлено перенаправление трафика на порт 22;
- активирована служба `ssh-custom` и перезагружены правила FirewallD;
- включено перенаправление IPv4-пакетов и настроен masquerading;
- проверено подключение по SSH через новый порт и доступ в Интернет с клиентской машины;
- подготовлены конфигурационные файлы и скрипт `firewall.sh` для автоматического провижининга.

# Контрольные вопросы

## 1. Где хранятся пользовательские файлы firewalld?

Пользовательские файлы служб и зон firewalld располагаются в каталоге:
`/etc/firewalld/`
Здесь находятся пользовательские версии конфигураций, перекрывающие системные файлы из `/usr/lib/firewalld/`.

## 2. Какую строку надо включить в пользовательский файл службы, чтобы указать порт TCP 2022?

Нужно добавить элемент порта:
`<port protocol="tcp" port="2022"/>`

## 3. Какая команда позволяет перечислить все службы, доступные в настоящее время на сервере?

Для вывода списка всех доступных служб используется команда:
`firewall-cmd --get-services`

## 4. В чём разница между NAT и masquerading?

**NAT** — общая технология трансляции сетевых адресов: подменяет IP-адреса и/или порты в пакетах при прохождении через маршрутизатор.

**Masquerading** — разновидность NAT, при которой исходящий трафик получает внешний IP-адрес интерфейса автоматически.
Используется, когда внешний IP может изменяться и не задаётся вручную.

## 5. Какая команда разрешает входящий трафик на порт 4404 и перенаправляет его в службу SSH по адресу 10.0.0.10?

Команда перенаправления с разрешением порта:
`firewall-cmd --add-forward-port=port=4404:proto=tcp:toaddr=10.0.0.10:toport=22`

## 6. Какая команда используется для включения маскарадинга для всех пакетов, выходящих в зону public?

Для активации masquerading применяется:
`firewall-cmd --zone=public --add-masquerade`
