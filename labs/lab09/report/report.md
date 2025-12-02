---
## Front matter
title: "Отчёт по лабораторной работе 9"
subtitle: "Настройка POP3/IMAP сервера"
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

Приобретение практических навыков по установке и простейшему конфигурированию POP3/IMAP-сервера.

# Выполнение

## Установка необходимых пакетов

На виртуальной машине `server` была выполнена установка пакетов `dovecot` и `telnet`.  
Процесс установки завершился успешно, что подтверждается итоговым сообщением:

![Установка пакетов dovecot и telnet](Screenshot_1.png){ #fig:004 width=85% }

В систему добавлены компоненты:

- dovecot-1:2.3.21-16.el10.x86_64  
- telnet-1:0.17-94.el10.x86_64

## Настройка Dovecot

В конфигурационном файле `/etc/dovecot/dovecot.conf` задан список протоколов, используемых Dovecot:

![Настройка протоколов IMAP и POP3](Screenshot_2.png){ #fig:005 width=80% }

Разрешённые протоколы: imap, pop3.

В файле `/etc/dovecot/conf.d/10-auth.conf` установлен механизм аутентификации plain:

![Указание механизма аутентификации plain](Screenshot_3.png){ #fig:006 width=70% }

Используемый механизм: plain.

В файле `/etc/dovecot/conf.d/auth-system.conf.ext` подтверждено использование:

- PAM для проверки паролей  
- passwd как источника данных о пользователях

![Настройка passdb и userdb](Screenshot_4.png){ #fig:007 width=75% }

В файле `/etc/dovecot/conf.d/10-mail.conf` указано использование Maildir в домашнем каталоге пользователя:

![Настройка каталога Maildir](Screenshot_5.png){ #fig:008 width=85% }

Каталог почты: `~/Maildir`.

В Postfix определён каталог Maildir для доставки входящей почты.

Для корректной работы служб POP3 и IMAP в `firewalld` разрешены соответствующие сервисы:

![Открытие портов POP3/IMAP в firewalld](Screenshot_6.png){ #fig:009 width=90% }

Активированы службы: pop3, pop3s, imap, imaps.  
После этого список разрешённых сервисов был успешно обновлён.

Восстановлен контекст безопасности для конфигурационных файлов:

Выполнено рекурсивное восстановление контекстов для `/etc`.

Почтовые службы были корректно перезапущены:  
Postfix — перезапущен, Dovecot — включён в автозапуск и запущен.

Все компоненты почтовой системы успешно настроены и функционируют.

## Проверка работы Dovecot

В почтовом клиенте Evolution были последовательно выполнены шаги по настройке учётной записи.  
На этапе ввода персональных данных указаны имя пользователя и почтовый адрес:

![Настройка идентификационной информации](Screenshot_7.png){ #fig:010 width=75% }

Для приёма почты выбран IMAP-сервер `mail.trseidaliev.net` с портом 143.  
Метод шифрования — STARTTLS, метод аутентификации — пароль:

![Настройка входящей почты IMAP](Screenshot_8.png){ #fig:011 width=75% }

Для исходящей почты указан SMTP-сервер `mail.trseidaliev.net` с портом 25.  
Шифрование отключено, аутентификация сервером не требуется:

![Настройка исходящей почты SMTP](Screenshot_9.png){ #fig:012 width=75% }

После завершения настройки почтовый клиент корректно отобразил входящие сообщения:

![Отправленные тестовые письма в Evolution](Screenshot_10.png){ #fig:013 width=85% }

На сервере выполнялось наблюдение за логами почтовой системы при помощи мониторинга системного журнала:

![Логи postfix при доставке писем](Screenshot_11.png){ #fig:014 width=90% }

По журналу видно:

- успешное подключение клиента к SMTP-службе;
- приём сообщения сервером;
- помещение письма в Maildir конкретного пользователя;
- очистку очереди доставки.

Серверный клиент `mail` показал наличие писем в каталоге Maildir пользователя:

![Просмотр писем средствами mail](Screenshot_12.png){ #fig:015 width=75% }

Также через POP3 с использованием Telnet удалось получить список сообщений, просмотреть письмо и удалить второе:

![Работа POP3 через telnet](Screenshot_13.png){ #fig:016 width=85% }

Сообщения корректно выдаются и удаляются, что подтверждает корректность работы Dovecot в роли POP3-сервера.

## Внесение изменений в настройки внутреннего окружения

Все конфигурационные файлы Dovecot были сохранены в каталог Vagrant-провижининга:

В файл mail.sh добавлены действия:

- установка Dovecot и Telnet;
- настройка правил firewall (`pop3`, `pop3s`, `imap`, `imaps`);
- конфигурация Postfix для работы с Maildir;
- перезапуск Postfix и запуск Dovecot.

![Изменённый provisioning-скрипт server/mail.sh](Screenshot_14.png){ #fig:017 width=80% }

На клиенте в файл `/vagrant/provision/client/mail.sh` добавлена установка почтового клиента Evolution:

![Изменённый provisioning-скрипт client/mail.sh](Screenshot_15.png){ #fig:018 width=70% }

# Заключение

В ходе практической работы была выполнена развёртка и настройка почтовой подсистемы на связке Postfix и Dovecot.  
Реализована корректная доставка и получение почты, проверенная через:

- почтовый клиент Evolution (IMAP/SMTP);
- просмотр почтового ящика на сервере средствами `mail`;
- применение утилиты `doveadm` для работы с почтовыми ящиками;
- проверку протокола POP3 через Telnet;
- анализ логов Postfix и Dovecot.

Также внесены необходимые изменения в provisioning-скрипты серверной и клиентской машин, что обеспечивает автоматическое развертывание рабочей почтовой инфраструктуры.

# Контрольные вопросы

## 1. За что отвечает протокол SMTP?

SMTP используется для **отправки почтовых сообщений**.  
Он обеспечивает передачу электронных писем от клиента к почтовому серверу и между почтовыми серверами.

## 2. За что отвечает протокол IMAP?

IMAP служит для **удалённого доступа к почтовым ящикам**, позволяя:

- просматривать структуру почтовых папок,
- работать с письмами непосредственно на сервере,
- синхронизировать состояние почты на нескольких устройствах.

## 3. За что отвечает протокол POP3?

POP3 предназначен для **получения почты с сервера** путём скачивания сообщений на локальный компьютер.  
Обычно письма удаляются с сервера после загрузки, если не указано обратное.

## 4. В чём назначение Dovecot?

Dovecot выполняет роль **IMAP/POP3-сервера**, обеспечивая:

- доступ пользователей к их почтовым ящикам,
- аутентификацию,
- хранение почтовых данных,
- работу с форматами Maildir и mbox.

Это быстрый, безопасный и широко используемый сервер для работы с почтовыми протоколами.

## 5. В каких файлах находятся настройки Dovecot? За что отвечает каждый?

Основные файлы:

- **/etc/dovecot/dovecot.conf** — базовый конфигурационный файл, определяет общие параметры и список активных протоколов.
- **/etc/dovecot/conf.d/10-auth.conf** — настройки аутентификации (методы входа, механизмы авторизации).
- **/etc/dovecot/conf.d/auth-system.conf.ext** — выбор источников данных о пользователях (`pam`, `passwd`, LDAP и др.).
- **/etc/dovecot/conf.d/10-mail.conf** — параметры хранения почтовых ящиков (Maildir, mbox, пути хранения).
- **/etc/dovecot/conf.d/10-logging.conf** — параметры логирования.
- **/etc/dovecot/conf.d/10-master.conf** — настройка служб, сокетов и взаимодействия с Postfix.

## 6. В чём назначение Postfix?

Postfix — это **MTA (Mail Transfer Agent)**, отвечающий за:

- приём входящих писем через SMTP,
- маршрутизацию и доставку сообщений,
- пересылку почты другим серверам,
- взаимодействие с локальной системой доставки (например, Maildir).

## 7. Какие методы аутентификации используются в Dovecot и чем отличаются?

Основные методы:

- **plain** — передача пароля в обычном виде (допустимо только внутри защищённых каналов).
- **login** — двухэтапная аутентификация, также передаёт пароль незашифрованным.
- **digest-md5, cram-md5** — пароли не передаются напрямую, используется хеширование.
- **PAM** — аутентификация с использованием системных механизмов Linux.
- **passwd** — извлечение информации о пользователях из `/etc/passwd`.

Основное различие заключается в способе хранения и передачи паролей: от открытого вида (plain) до хешированных механизмов (cram-md5).

## 8. Пример заголовка письма с пояснениями полей

```
Return-Path: [user@example.net](mailto:user@example.net)      ← адрес отправителя
Received: by server.example.net      ← путь прохождения письма
Date: Tue, 10 Dec 2025 14:20:00 +0300 ← дата отправки
From: User [user@example.net](mailto:user@example.net)        ← имя и адрес отправителя
To: [admin@example.net](mailto:admin@example.net)                ← адрес получателя
Subject: Test message                ← тема письма
Message-ID: [12345@example.net](mailto:12345@example.net)      ← уникальный идентификатор письма
```

Заголовки содержат ключевую информацию: от автора письма до маршрута прохождения по сети.

## 9. Примеры команд для работы с почтовыми протоколами через терминал

Работа по POP3 через telnet:

```
telnet mail.example.net 110
user username
pass password
list                         ← список писем
retr 1                       ← получение письма №1
dele 2                       ← удаление письма №2
quit                         ← завершение работы
```

Пример отправки письма вручную через SMTP:

```
telnet mail.example.net 25
ehlo example.net
mail from:[user@example.net](mailto:user@example.net)
rcpt to:[admin@example.net](mailto:admin@example.net)
data
Subject: Test mail
Hello!
.
quit
```

## 10. Примеры работы с doveadm

Просмотр списка почтовых ящиков:

```
doveadm mailbox list -u user
```

Просмотр количества писем в папке:

```
doveadm mailbox status -u user messages INBOX
```

Поиск писем по критерию:

```
doveadm search -u user mailbox INBOX subject test
```

Удаление письма по UID:

```
doveadm expunge -u user mailbox INBOX uid 5
```

Эти команды позволяют администратору управлять содержимым почтовых ящиков без прямого доступа к файлам Maildir.

