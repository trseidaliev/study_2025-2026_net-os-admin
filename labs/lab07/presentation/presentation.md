---
lang: ru-RU
title: Администрирование системных подсистем
subtitle: Настройка FirewallD, Port Forwarding и Masquerading
author:
  - Сейдалиев Тагиетдин Ровшенович
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 26 ноября 2025

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

Получить практические навыки:

- создания пользовательских служб FirewallD;
- настройки перенаправления портов;
- включения masquerading;
- интеграции сетевых конфигураций в систему провижининга Vagrant.

# Создание пользовательской службы FirewallD

## Копирование и анализ службы

Создана копия стандартного файла `ssh.xml` → `ssh-custom.xml`.  
Проанализирована структура XML-файла службы.

![ssh-custom.xml](Screenshot_1.png){ width=85% }

## Модификация службы

Изменён порт с 22 на 2022, обновлено описание службы.

![Изменение порта](Screenshot_2.png){ width=70% }

## Перезагрузка и проверка

После reload служба появилась в списке доступных, но не активированных.

![Добавление службы](Screenshot_3.png){ width=90% }

## Активация службы

`ssh-custom` добавлена в активные службы и сохранена в постоянной конфигурации.

# Перенаправление портов

## Forwarding 2022 → 22

Реализовано перенаправление порта 2022 на стандартный порт SSH.

![Forward-port](Screenshot_4.png){ width=80% }

## Проверка с клиента

Успешное подключение по SSH через порт 2022.

![SSH через 2022](Screenshot_5.png){ width=80% }

# Настройка forwarding и masquerading

## IPv4 forwarding

Включена поддержка перенаправления IPv4-пакетов через конфигурацию sysctl.

![IPv4 forwarding](Screenshot_6.png){ width=90% }

## Masquerading

В зоне `public` активирован masquerading.  
После reload проверен доступ в Интернет с клиентской машины.

![Проверка интернета](Screenshot_7.png){ width=90% }

# Итоги работы

## Основные результаты

- создана пользовательская служба firewall с портом 2022;
- настроено перенаправление портов и masquerading;
- обеспечена корректная работа SSH через новый порт;
- выполнена интеграция сетевых правил в Vagrant-провижининг.
