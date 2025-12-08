---
## Front matter
title: "Отчёт по лабораторной работе 14"
subtitle: " Настройка файловых служб Samba"
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

Приобретение навыков настройки доступа групп пользователей к общим ресурсам по протоколу SMB.

# Выполнение

## Настройка сервера Samba


На сервере были установлены пакеты Samba и утилиты CIFS. После выполнения установки система вывела список всех установленных компонентов.

![Установка пакетов Samba](Screenshot_1.png){ #fig:001 width=90% }

Создана группа `sambagroup` с GID `1010`, затем пользователь `trseidaliev` был добавлен в эту группу.

Создан каталог `/srv/sambashare`, который будет использоваться для размещения файлов, доступных через Samba.

В файле `/etc/samba/smb.conf` изменён параметр рабочей группы на:

```
workgroup = TRSEIDALIEV-NET
```

В конец файла была добавлена секция общего ресурса:

```
[sambashare]
comment = My Samba Share
path = /srv/sambashare
write list = @sambagroup
```

![Файл smb.conf с добавленной секцией sambashare](Screenshot_2.png){ #fig:002 width=80% }

Команда проверки конфигурации показала, что ошибок в файле `smb.conf` нет.

Служба SMB была запущена, добавлена в автозагрузку, и её статус отображается как активный.

![Статус службы smb.service](Screenshot_3.png){ #fig:003 width=90% }

Через `smbclient` был выполнен просмотр доступных ресурсов сервера. Анонимное подключение прошло успешно.

Файл `samba.xml` из каталога firewalld показал, какие порты и службы относятся к Samba.

![Открытый профиль Samba в firewalld](Screenshot_4.png){ #fig:004 width=90% }

В межсетевом экране были разрешены службы Samba, настройки сохранены, после чего конфигурация была перезагружена.

Каталогу `/srv/sambashare` была назначена группа `sambagroup`, а группе даны права на чтение, запись и выполнение.

После применения контекста `samba_share_t` каталог был перезаписан, и его контекст был изменён:

![Контекст SELinux после изменения](Screenshot_5.png){ #fig:005 width=85% }

SELinux был настроен на разрешение чтения и записи экспортируемых ресурсов Samba.

Были просмотрены UID пользователя, а также группы, в которые он входит.

![Информация о пользователе trseidaliev](Screenshot_6.png){ #fig:006 width=90% }

Пользователь `trseidaliev` создал файл в каталоге `/srv/sambashare`, что подтвердило корректность настроек прав и SELinux.

![Создание файла пользователем](Screenshot_7.png){ #fig:007 width=85% }

Пользователь `trseidaliev` был добавлен как SMB-пользователь и получил SMB-пароль.

![Добавление пользователя в Samba](Screenshot_8.png){ #fig:008 width=60% }

## Настройка Samba на клиенте

На клиентской системе были установлены пакеты `samba-client` и `cifs-utils`.

![Профиль samba-client в firewalld](Screenshot_9.png){ #fig:009 width=90% }

На клиенте были разрешены службы Samba Client, и конфигурация была перезагружена.

На клиенте была создана группа `sambagroup` с тем же GID `1010`, и пользователь `trseidaliev` добавлен в неё.

В файле `/etc/samba/smb.conf` был изменён параметр рабочей группы:

```
workgroup = TRSEIDALIEV-NET
```

![Изменение smb.conf на клиенте](Screenshot_10.png){ #fig:010 width=85% }

На клиенте была выполнена проверка доступности общих ресурсов Samba с использованием команды `smbclient -L //server`.  
Поскольку пароль не вводился, подключение выполнилось **под анонимной учётной записью**.

Повторная проверка была выполнена с указанием имени пользователя. В этом случае доступ осуществлялся **под учётной записью trseidaliev**.

![Просмотр ресурсов Samba под пользователем](Screenshot_11.png){ #fig:011 width=90% }

На клиенте была создана директория `/mnt/samba`, предназначенная для подключения общего ресурса.

Ресурс `//server/sambashare` был смонтирован в каталог `/mnt/samba` с использованием имени пользователя `trseidaliev`.

![Монтирование Samba-ресурса](Screenshot_12.png){ #fig:012 width=90% }

После монтирования пользователь `trseidaliev` перешёл в каталог `/mnt/samba` и создал тестовый файл, что подтвердило корректность прав доступа.

![Создание файла на клиенте](Screenshot_13.png){ #fig:013 width=90% }

В каталоге `/etc/samba/` был создан файл `smbusers` с правами доступа `600`.  
В него были помещены учётные данные пользователя Samba:

```
username=trseidaliev
password=123456
```

![Файл smbusers](Screenshot_14.png){ #fig:014 width=60% }

В файл `/etc/fstab` была добавлена строка для автоматического подключения Samba-ресурса:

```
//server/sambashare /mnt/samba cifs user,rw,uid=trseidaliev,gid=sambagroup,credentials=/etc/samba/smbusers,_netdev 0 0
```

![Файл fstab с записью для Samba](Screenshot_15.png){ #fig:015 width=90% }

Команда `mount -a` корректно примонтировала ресурс.  
Результат можно увидеть в выводе команды `mount`:

![Успешное монтирование через fstab](Screenshot_16.png){ #fig:016 width=90% }

На сервере в каталоге `/srv/sambashare` появился файл, созданный клиентом, что подтверждает рабочую синхронизацию:

![Появление файлов клиента на сервере](Screenshot_17.png){ #fig:017 width=80% }

## Создание скриптов провижининга для серверной и клиентской машины

В каталоге `/vagrant/provision/server/` был создан каталог `smb` и подготовлены конфигурационные файлы.  
Скрипт автоматизирует установку пакетов, настройку SELinux, создание каталога и запуск службы SMB.

![Содержимое smb.sh на сервере](Screenshot_18.png){ #fig:018 width=85% }

Аналогичным образом в каталоге `/vagrant/provision/client/` был создан скрипт для настройки клиента, монтирования ресурса и применения конфигураций Samba.

![Содержимое smb.sh на клиенте](Screenshot_19.png){ #fig:019 width=85% }

# Заключение

В ходе выполнения работы:

- выполнена установка и настройка файлового сервера Samba на виртуальной машине `server`;
- создан общий ресурс `/srv/sambashare`, настроены группы, права доступа и контекст SELinux;
- добавлен пользователь `trseidaliev` в локальную систему и базу пользователей Samba;
- настроен межсетевой экран сервера для обеспечения корректной работы SMB-служб;
- произведена проверка доступности ресурсов как анонимным пользователем, так и под учётной записью `trseidaliev`;
- на клиентской машине установлены необходимые пакеты и выполнена корректная настройка рабочей группы Samba;
- выполнено ручное и автоматическое (через `/etc/fstab`) монтирование ресурса на клиенте с последующей проверкой возможности записи;
- подготовлены конфигурационные файлы и скрипты провижининга для автоматизации настройки Samba на обеих виртуальных машинах.

# Контрольные вопросы

## 1. Какова минимальная конфигурация для smb.conf для создания общего ресурса, который предоставляет доступ к каталогу /data?

Минимальная конфигурация включает только глобальную секцию и определение ресурса:

```
[global]
workgroup = WORKGROUP

[data]
path = /data
read only = no
```

Такого набора параметров достаточно, чтобы Samba экспортировала каталог `/data` и разрешала запись.

## 2. Как настроить общий ресурс, который даёт доступ на запись всем пользователям, имеющим права на запись в файловой системе Linux?

Достаточно указать, что Samba должна учитывать права файловой системы:

```
[data]
path = /data
read only = no
unix extensions = yes
```

Поскольку Samba использует Unix-права, любой пользователь, имеющий запись в `/data` на уровне ОС, сможет писать в ресурс.

## 3. Как ограничить доступ на запись к ресурсу только членам определённой группы?

Используется параметр `write list`, куда указывается группа:

```
[data]
path = /data
read only = no
write list = @groupname
```

Только пользователи из группы `groupname` смогут выполнять запись.

## 4. Какой переключатель SELinux нужно использовать, чтобы позволить пользователям получать доступ к домашним каталогам на сервере через SMB?

Нужно включить boolean-переключатель:

```
setsebool -P samba_enable_home_dirs 1
```

Он разрешает доступ Samba к домашним каталогам пользователей.

## 5. Как ограничить доступ к определённому ресурсу только узлам из сети 192.168.10.0/24?

В секции ресурса указывают параметры `hosts allow` и при необходимости `hosts deny`:

```
[data]
path = /data
hosts allow = 192.168.10.0/24
hosts deny = 0.0.0.0/0
```

## 6. Какую команду можно использовать, чтобы отобразить список всех пользователей Samba на сервере?

Используется команда:

```
pdbedit -L
```

Она выводит список учётных записей Samba.

## 7. Что нужно сделать пользователю для доступа к ресурсу, который настроен как многопользовательский ресурс?

Пользователь должен:

- иметь локальную учётную запись в системе Linux;
- быть добавлен в базу пользователей Samba;
- получить пароль SMB.

## 8. Как установить общий ресурс Samba в качестве многопользовательской учётной записи, где пользователь alice используется как минимальная учётная запись пользователя?

Используется параметр `force user`:

```
[data]
path = /data
read only = no
force user = alice
```

Все операции на ресурсе будут выполняться от имени пользователя `alice`.

## 9. Как можно запретить пользователям просматривать учётные данные монтирования Samba в файле /etc/fstab?

Учётные данные выносят в отдельный файл с ограниченными правами:

1. создаётся файл:

```
/etc/samba/credentials
```

2. задают ограничения:

```
chmod 600 /etc/samba/credentials
```

3. в fstab указывают:

```
credentials=/etc/samba/credentials
```

Таким образом обычные пользователи не увидят пароль.

## 10. Какая команда позволяет перечислить все экспортируемые ресурсы Samba, доступные на определённом сервере?

Используется команда:

```
smbclient -L //server
```

Она выводит все открытые ресурсы сервера Samba.

