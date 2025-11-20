---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка и анализ работы DNS-сервера BIND
author:
  - Сейдалиев Тагиетдин Ровшенович
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 19 ноября 2025

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

Освоить установку, конфигурирование и анализ работы DNS-сервера BIND,  
а также автоматизацию его настройки во внутреннем окружении ВМ.

# Установка DNS-сервера

## Проверка работы внешнего DNS

- Установлены пакеты bind и bind-utils  
- Выполнен тест dig для внешнего DNS  
- Проанализирована структура DNS-ответа

![dig внешний DNS](Screenshot_1.png){ width=70% }

# Конфигурирование кэширующего DNS

## Конфигурация resolv.conf и named.conf

- Использование внешнего nameserver  
- Настройки listen-on и allow-query  
- Структура служебных файлов

![named.conf](Screenshot_2.png){ width=75% }

## Файлы корневых и локальных зон

- named.ca — корневые DNS-серверы  
- named.localhost — локальная зона  
- named.loopback — обратная зона

![root и local zone](Screenshot_4.png){ width=80% }

# Проверка работы локального DNS

## dig через 127.0.0.1

- Корректный ответ сервера  
- Работает кэширование запросов

![dig 127.0.0.1](Screenshot_5.png){ width=75% }

## Настройка DNS через nmcli

- Включён ignore-auto-dns  
- Установлен DNS = 127.0.0.1  
- resolv.conf обновлён

![nmcli настройка](Screenshot_6.png){ width=80% }

# Настройка доступа сети

## Правки named.conf

- Прослушивание на всех интерфейсах  
- Разрешение запросов от сети 192.168.0.0/16

![listen/allow-query](Screenshot_7.png){ width=80% }

## Настройка firewall и проверка порта

- Открыт порт 53 TCP/UDP  
- named слушает порт корректно

![lsof порт 53](Screenshot_8.png){ width=75% }

# Настройка первичного DNS-сервера

## Подключение файла зон

- Скопирован шаблон зон  
- Добавлен include для файла trseidalev.net

![include зоны](Screenshot_9.png){ width=75% }

## Прямая и обратная зона

- Создана зона trseidalev.net  
- Создана обратная зона 1.168.192.in-addr.arpa

![zone файлы](Screenshot_10.png){ width=80% }

# Файлы мастер-зон

## Прямая зона

- SOA-запись  
- NS и A-записи  
- $ORIGIN trseidalev.net.

![fz зона](Screenshot_11.png){ width=75% }

## Обратная зона

- PTR-записи  
- Корректный SOA  
- Соответствие IP ↔ имя

![rz зона](Screenshot_12.png){ width=75% }

# SELinux и права

## Настройка SELinux

- Восстановлены контексты  
- Разрешена запись в мастер-зоны  
- Проверены bool-переключатели

![selinux вывод](Screenshot_13.png){ width=80% }

# Анализ работы DNS

## Проверка dig

- ns.trseidalev.net → 192.168.1.1  
- Корректная A-запись

![dig ns.trseidalev.net](Screenshot_14.png){ width=80% }

## Проверка host

- Листинг зоны  
- ANY-запросы  
- PTR-записи

![host проверки](Screenshot_15.png){ width=95% }

# Итоги работы

## Основные результаты

- Настроен кэширующий и первичный DNS-сервер BIND  
- Созданы прямая и обратная зоны  
- DNS успешно обслуживает запросы  
- Настройки автоматизированы через dns.sh  
- Проверены dig, host и работа SELinux
