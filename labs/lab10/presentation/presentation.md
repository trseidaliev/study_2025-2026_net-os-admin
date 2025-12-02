---
## Front matter
lang: ru-RU
title: Администрирование системных подсистем
subtitle: Настройка SMTP, LMTP, SASL и TLS на Postfix + Dovecot
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

Получение практических навыков настройки SMTP-сервера Postfix и Dovecot: LMTP, аутентификация SASL, TLS-шифрование.

# Настройка LMTP в Dovecot

## Добавление поддержки LMTP

Добавляем LMTP в конфигурацию Dovecot.

![LMTP протокол](Screenshot_1.png){ width=80% }

## Настройка LMTP-сервиса

Определяем сокет LMTP для взаимодействия Postfix и Dovecot.

![Сервис LMTP](Screenshot_2.png){ width=75% }

## Формат имени пользователя

Задаём формат логина без доменной части.

![auth_username_format](Screenshot_3.png){ width=80% }

## Логи успешной LMTP-доставки

Подтверждение доставки письма в INBOX через LMTP.

![Логи LMTP](Screenshot_4.png){ width=95% }

## Проверка Maildir

Письмо отображается в пользовательском почтовом ящике.

![Maildir](Screenshot_5.png){ width=80% }

# Настройка SMTP-аутентификации

## Служба auth в Dovecot

Настраивается сокет для SASL-аутентификации Postfix.

![auth service](Screenshot_6.png){ width=70% }

## Включение SASL в Postfix

Postfix использует Dovecot как механизм SASL.

![postconf sasl](Screenshot_7.png){ width=90% }

## Ограничения приёма писем

Настраиваются правила защиты от open relay.

![master.cf](Screenshot_8.png){ width=90% }

## Проверка аутентификации

Пользователь успешно проходит SMTP AUTH.

![telnet auth](Screenshot_9.png){ width=90% }

# Настройка SMTP over TLS

## TLS-конфигурация Postfix

Postfix использует сертификаты Dovecot для TLS.

![TLS настройка](Screenshot_10.png){ width=100% }

## Служба submission на порту 587

Создаётся защищённый SMTP-сервис с обязательным шифрованием.

![submission](Screenshot_11.png){ width=95% }

## STARTTLS через openssl

Проверяется установление TLS-сессии и успешный AUTH.

![openssl test](Screenshot_12.png){ width=95% }

## Настройка SMTP в Evolution

Почтовый клиент успешно использует порт 587 и STARTTLS.

![evolution smtp](Screenshot_13.png){ width=80% }

## Проверка доставки писем

Письма успешно доставляются через защищённый SMTP.

![Почтовый клиент](Screenshot_14.png){ width=80% }

## Логи Postfix и Dovecot

Журнал фиксирует успешную обработку и доставку писем.

![maillog](Screenshot_15.png){ width=95% }

# Заключение

В ходе выполнения работы:

- настроен SMTP-сервер Postfix с поддержкой аутентификации пользователей через Dovecot;
- реализована передача почты посредством LMTP-сервиса Dovecot;
- включена и проверена работа TLS-шифрования для SMTP (порт 587, STARTTLS);
- выполнена настройка firewall для служб smtp и smtp-submission;
- протестирована SMTP-аутентификация как через telnet/openssl, так и через почтовый клиент Evolution;
- обновлены конфигурации Postfix и Dovecot в каталоге provisioning для автоматизации развёртывания.
