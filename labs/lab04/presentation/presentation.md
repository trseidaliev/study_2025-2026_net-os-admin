---
lang: ru-RU
title: Администрирование системных подсистем  
subtitle: Настройка и виртуальный хостинг HTTP-сервера Apache  
author:
  - Сейдалиев Тагиетдин Ровшенович
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 23 ноября 2025
toc: false
slide_level: 2
aspectratio: 169
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цель работы

## Основная цель

Получить практические навыки установки, настройки и автоматизации работы HTTP-сервера Apache, включая виртуальный хостинг и подготовку среды для последующего провижининга.

# Выполнение работы

## Установка пакетов

- Установлена группа пакетов *Basic Web Server*  
- Apache и необходимые модули загружены из репозитория  
- Произведена начальная настройка службы httpd

![Конфигурация /etc/httpd](Screenshot_1.png){ width=75% }

## firewall-cmd

- Разрешён доступ по HTTP  

![Логи firewall и httpd](Screenshot_2.png){ width=80% }

## Стартовая страница Apache

В браузере  открыта тестовая страница по адресу: `http://192.168.1.1`

![Тестовая страница Apache](Screenshot_3.png){ width=80% }

## error_log и access_log

- Просмотрены ошибки запуска  
- Зафиксированы успешные запросы клиента  
- Проверено корректное формирование HTTP-ответов

![Логи доступа и ошибок](Screenshot_4.png){ width=95% }

## Прямая и обратная зоны

- *A-запись* `www.trseidaliev.net`  

![Прямая зона](Screenshot_5.png){ width=80% }

- *PTR-запись* для IP-адреса  

## Прямая и обратная зоны

![Обратная зона](Screenshot_6.png){ width=80% }

## Конфигурация server.trseidaliev.net

- Собственный DocumentRoot  
- Логи доступа и ошибок  
- Отдельный конфигурационный файл

![server.trseidaliev.net.conf](Screenshot_7.png){ width=70% }

## Конфигурация www.trseidaliev.net

- Собственная директория сайта  
- Раздельные журналы

![www.trseidaliev.net.conf](Screenshot_8.png){ width=70% }

## Индексные страницы

- Созданы каталоги сайтов  
- Размещены индивидуальные приветственные страницы

![index.html server](Screenshot_9.png){ width=70% }

## Подготовка окружения

- Назначены владельцы каталогов  
- Восстановлены контексты SELinux  
- HTTP-сервер перезапущен

![SELinux и рестарт httpd](Screenshot_11.png){ width=90% }

## server.trseidaliev.net

![Просмотр сайта server](Screenshot_12.png){ width=75% }

## www.trseidaliev.net

![Просмотр сайта www](Screenshot_13.png){ width=75% }


# Итоги работы

## Основные результаты

- Установлен и настроен Apache  
- Реализован виртуальный хостинг  
- Настроены прямые и обратные DNS-зоны  
- Созданы отдельные сайты для разных доменных имён  
- Выполнена подготовка среды для автоматического провижининга  
- Проверена доступность обоих виртуальных сайтов