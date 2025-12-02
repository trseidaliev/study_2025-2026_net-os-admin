---
## Front matter
lang: ru-RU
title: Администрирование системных подсистем
subtitle: Настройка POP3/IMAP сервера Dovecot + Postfix
author:
  - Сейдалиев Тагиетдин Ровшенович
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 30 ноября 2025
## Formatting pdf
toc: false
slide_level: 2
aspectratio: 169
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цель работы

## Основная цель

Настроить и проверить работу почтовых сервисов POP3 и IMAP.

# Установка компонентов

## Установка необходимых пакетов

Были установлены пакеты Dovecot и Telnet.

![Установка пакетов](Screenshot_1.png){ width=80% }

# Настройка Dovecot

## Протоколы почты

Активированы протоколы IMAP и POP3.

![Протоколы](Screenshot_2.png){ width=80% }

## Аутентификация

Выбран метод аутентификации plain.

![auth_mechanisms](Screenshot_3.png){ width=70% }

## Источники учётных данных

Использованы PAM и passwd как источники данных о пользователях.

![auth-system.conf.ext](Screenshot_4.png){ width=70% }

## Формат почтовых ящиков

Почтовые данные хранятся в формате Maildir.

![Maildir](Screenshot_5.png){ width=85% }

# Настройка Postfix и firewall

## Настройка доставки почты

Postfix отправляет почту в каталог Maildir.

![firewalld](Screenshot_6.png){ width=90% }

# Проверка работы системы

## Настройка Evolution

Почтовый клиент настроен на работу с сервером.

![Настройка Evolution](Screenshot_7.png){ width=70% }

## Настройка IMAP

IMAP-сервер настроен корректно.

![IMAP настройки](Screenshot_8.png){ width=70% }

## Настройка SMTP

SMTP-сервер принимает исходящую почту.

![SMTP настройки](Screenshot_9.png){ width=70% }

## Проверка получения почты

Письма успешно приняты в Evolution.

![Письма в Evolution](Screenshot_10.png){ width=90% }

# Наблюдение и диагностика

## Логи Postfix

Логи подтверждают доставку писем.

![maillog](Screenshot_11.png){ width=90% }

## Просмотр почты через терминал

Письма отображаются в Maildir.

![mail](Screenshot_12.png){ width=70% }

## Проверка POP3 через Telnet

POP3 работает корректно.

![POP3 Telnet](Screenshot_13.png){ width=80% }

# Итоги работы

## Основные результаты

- Настроены и запущены почтовые сервисы Dovecot (IMAP/POP3) и Postfix (SMTP).
- Выполнена проверка доставки, получения и чтения писем различными способами.
- Настроен почтовый клиент Evolution для работы с сервером.
- Проверена работа POP3 через Telnet и просмотр Maildir через терминал.
- Логи Postfix подтвердили корректность маршрутизации и доставки сообщений.
- Обновлены provisioning-скрипты для автоматизации развёртывания почтовой системы.
