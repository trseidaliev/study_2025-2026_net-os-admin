---
## Front matter
lang: ru-RU
title: Администрирование системных подсистем
subtitle: Настройка DHCP-сервера Kea и интеграция с DNS (Bind9 + DDNS)
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

Приобретение практических навыков настройки DHCP-сервера Kea  
и интеграции его с DNS Bind9, включая механизм динамических обновлений DDNS.

# Конфигурирование DHCP-сервера

## Настройка параметров домена

- Обновлены `domain-name`, `domain-search`
- DNS-сервер: `192.168.1.1`
- Внесены изменения в файл `/etc/kea/kea-dhcp4.conf`

![Фрагмент kea-dhcp4.conf с параметрами домена и DNS](Screenshot_1.png){ #fig:001 width=80% }

## Настройка подсети DHCP

- Сеть: `192.168.1.0/24`
- Диапазон: `192.168.1.30–192.168.1.199`
- Шлюз: `192.168.1.1`

![Конфигурация подсети DHCP](Screenshot_2.png){ #fig:002 width=80% }

## Проверка работы службы DHCP

- Проверка корректности конфигурации  
- Перезапуск сервиса  
- Включение автозагрузки

![Проверка и включение службы kea-dhcp4](Screenshot_3.png){ #fig:003 width=95% }

# Настройка DNS-зон

## Прямая зона

Добавлена A-запись для DHCP-сервера и обновлён серийный номер зоны.

![Файл прямой зоны после изменения](Screenshot_4.png){ #fig:004 width=70% }

## Обратная зона

Добавлена PTR-запись и обновлён SOA serial.

![Файл обратной зоны](Screenshot_5.png){ #fig:005 width=80% }

# Проверка сетевого взаимодействия

## Проверка имени DHCP-узла

![Проверка ping по имени dhcp.<логин>.net](Screenshot_6.png){ #fig:006 width=80% }

## Настройка firewall и SELinux

- Разрешена служба DHCP  
- Восстановлены контексты SELinux `/etc`, `/var/named`, `/var/lib/kea`

![Изменения в firewall и SELinux](Screenshot_7.png){ #fig:007 width=85% }

# Анализ работы DHCP-сервера

## Скрипт маршрутизации клиента

![Скрипт настройки маршрутизации клиента](Screenshot_8.png){ #fig:008 width=70% }

## Получение IP-адреса клиентом

![Вывод ifconfig на клиенте](Screenshot_9.png){ #fig:009 width=80% }

## Журнал аренд Kea

![Содержимое файла kea-leases4.csv](Screenshot_10.png){ #fig:010 width=90% }

# Настройка DDNS

## Генерация TSIG-ключа

![Создание ключа DHCP_UPDATER](Screenshot_11.png){ #fig:011 width=90% }

## Включение ключа в named.conf

![Включение ключа в named.conf](Screenshot_12.png){ #fig:013 width=80% }

## Разрешения на обновления зоны

![Разрешение обновлений в прямой зоне](Screenshot_13.png){ #fig:014 width=80% }

## Разрешения на обновления зоны

![Разрешение обновлений в обратной зоне](Screenshot_14.png){ #fig:015 width=80% }

## Настройка tsig-keys.json

![Файл tsig-keys.json](Screenshot_15.png){ #fig:016 width=80% }

## Настройка kea-dhcp-ddns.conf

![Фрагмент kea-dhcp-ddns.conf](Screenshot_16.png){ #fig:017 width=90% }

## Запуск службы DDNS

![Запуск службы kea-dhcp-ddns](Screenshot_17.png){ #fig:018 width=90% }

## Включение DDNS в DHCP4

![Проверка и запуск Kea DHCP4](Screenshot_18.png){ #fig:019 width=90% }

# Проверка DDNS

## Проверка A-записи, созданной автоматически

![Результат выполнения dig](Screenshot_19.png){ #fig:019 width=80% }

# Итоги работы

## Основные результаты

- Настроен DHCP-сервер Kea с автоматической выдачей IP  
- Настроены прямые и обратные зоны Bind9  
- Реализован механизм DDNS с TSIG  
- Проверены A- и PTR-записи, создаваемые автоматически  
- Перенесены конфигурации в структуру Vagrant  
- Создан сценарий `dhcp.sh` для автоматического развёртывания

