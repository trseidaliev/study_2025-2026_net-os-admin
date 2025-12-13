---
lang: ru-RU
title: "Администрирование системных подсистем"
subtitle: "Fail2ban: базовая защита от brute-force атак"
author:
  - "Сейдалиев Тагиетдин Ровшенович"
institute:
  - "Российский университет дружбы народов, Москва, Россия"
date: 11 декабря 2025
toc: false
slide_level: 2
aspectratio: 169
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цель работы

## Основная цель

Получить навыки установки, настройки и проверки работы Fail2ban  
для защиты сервера от атак типа «brute force».

# Установка Fail2ban

## Установка и запуск сервиса

- Установка пакета Fail2ban  
- Включение автозапуска сервиса  
- Проверка логов запуска

![Установка Fail2ban](Screenshot_1.png){ width=80% }

## Установка и запуск сервиса

![Логи Fail2ban](Screenshot_2.png){ width=80% }

# Настройка Fail2ban

## Локальная конфигурация SSH

- Создание файла *customisation.local*  
- Настройка:
  - bantime = 3600  
  - защита SSH  
  - защита SSH DDoS  
  - включение selinux-ssh

![Конфигурация SSH](Screenshot_3.png){ width=55% }

## Активированные jails SSH

![Jails SSH](Screenshot_4.png){ width=80% }

# Защита HTTP

## Включённые jails Apache

- apache-auth  
- apache-badbots  
- apache-noscript  
- apache-overflows  
- apache-nohome  
- apache-botsearch  
- apache-fakegooglebot  
- apache-modsecurity  
- apache-shellshock

![HTTP-конфигурация](Screenshot_5.png){ width=55% }

## Логи запуска HTTP-защиты

![Jails HTTP](Screenshot_6.png){ width=80% }

# Защита почтовых служб

## Включённые почтовые jails

- postfix  
- postfix-rbl  
- dovecot  
- postfix-sasl  

![Почтовые jails](Screenshot_7.png){ width=55% }

## Активация почтовых jails

![Запуск почтовых jails](Screenshot_8.png){ width=80% }

# Проверка работы Fail2ban

## Статус сервиса и jails

![Общий статус Fail2ban](Screenshot_9.png){ width=80% }

## Снижение maxretry и попытки взлома

![Попытки входа SSH](Screenshot_9.png){ width=80% }

## Блокировка клиента

![Заблокированный IP](Screenshot_11.png){ width=80% }

## Разблокировка IP

![Разблокировка](Screenshot_11.png){ width=80% }

# Исключение IP из блокировки

## Добавление ignoreip

![ignoreip](Screenshot_12.png){ width=55% }

## Логи игнорирования IP

![Ignore log](Screenshot_13.png){ width=80% }

# Итоги работы

## Основные результаты

- Установлен и настроен Fail2ban  
- Активирована защита SSH, HTTP и почтовых служб  
- Проверена блокировка и разблокировка IP  
- Добавлен игнорируемый IP клиента  
- Проанализированы логи и работа jails  
- Настроена автоматизация конфигурации Fail2ban  
- Подготовлен скрипт для провижининга
