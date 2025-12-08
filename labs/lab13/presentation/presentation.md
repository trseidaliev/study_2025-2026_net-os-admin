---
lang: ru-RU
title: Администрирование системных подсистем
subtitle: Настройка сервера и клиента NFSv4
author:
  - Сейдалиев Тагиетдин Ровшенович
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 08 декабря 2025

toc: false
slide_level: 2
aspectratio: 169
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цель работы

## Основная цель

Приобрести навыки настройки сервера NFSv4, включая работу с SELinux, firewall, bind-монтированием и автоматическим подключением ресурсов на клиенте.

# Настройка сервера

## Установка и подготовка окружения

- Установка `nfs-utils`  
- Создание корневого каталога `/srv/nfs`  
- Настройка доступа в `/etc/exports`

![Экспорт каталога /srv/nfs](Screenshot_2.png){ width=55% }

## Настройка SELinux и запуск NFS

- Назначение контекста `nfs_t`  
- Применение `restorecon`  
- Запуск и автозагрузка `nfs-server.service`  
- Разрешение сервиса `nfs` в firewall

![SELinux и запуск NFS](Screenshot_3.png){ width=90% }

# Проверка работы клиента

## Ошибка при первом подключении

`showmount` → ошибка RPC из-за заблокированных служб `rpcbind` и `mountd`.

![Ошибка showmount](Screenshot_4.png){ width=80% }

## Проверка после отключения firewall

Firewall отключён → `showmount` успешно отображает экспортируемые каталоги.

![Успешный showmount](Screenshot_6.png){ width=55% }

# Настройка firewall

## Анализ используемых портов

Службы, необходимые для работы NFS:

- `nfs`  
- `rpc-bind`  
- `mountd`

![Просмотр служб TCP/UDP](Screenshot_5.png){ width=90% }

## Разрешение служб

Разрешение необходимых сервисов в firewall даёт стабильный доступ NFS-клиента.

# Монтирование NFS на клиенте

## Ручное подключение

- Создание `/mnt/nfs`  
- Монтирование ресурса `server:/srv/nfs`  

![Монтирование /mnt/nfs](Screenshot_8.png){ width=90% }

## Автоматическое подключение

Добавлена запись в `/etc/fstab`:

```
server.trseidaliev.net:/srv/nfs /mnt/nfs nfs _netdev 0 0
```

![fstab клиента](Screenshot_7.png){ width=80% }

# Экспорт веб-контента

## Подготовка каталога

- Создание `/srv/nfs/www`  
- Bind-монтирование `/var/www`  

![bind /var/www](Screenshot_10.png){ width=90% }

## Экспорт каталога

Добавление в `/etc/exports`:

```
/srv/nfs/www 192.168.0.0/16(rw)
```

![Экспорт www](Screenshot_9.png){ width=45% }

# Проверка на клиенте

Каталог `www` появляется в `/mnt/nfs`.

![Каталог www на клиенте](Screenshot_12.png){ width=55% }

# Пользовательские каталоги

## Создание каталога common

Пользователь создаёт каталог:

- `~/common`
- права доступа `700`
- файл `trseidaliev@server.txt`

![Каталог common](Screenshot_13.png){ width=80% }

## Экспорт личного каталога

Добавление в `/etc/exports`:

```
/srv/nfs/home/trseidaliev 192.168.0.0/16(rw)
```

![Экспорт пользовательского каталога](Screenshot_14.png){ width=70% }

## Bind-монтирование и автозагрузка

Запись в `/etc/fstab`:

```
/home/trseidaliev/common /srv/nfs/home/trseidaliev none bind 0 0
```

![fstab с пользовательским каталогом](Screenshot_15.png){ width=90% }

# Проверка работы пользователя на клиенте

## Пользовательские файлы

- Создание файла `trseidaliev@client.txt`
- Видны оба файла: от сервера и от клиента

![Работа с файлами пользователя](Screenshot_17.png){ width=95% }

## Проверка на сервере

Каталог содержит оба файла:

![Проверка на сервере](Screenshot_18.png){ width=80% }

# Итоги работы

## Основные результаты

- Настроен сервер NFSv4  
- Реализованы экспорты системных и пользовательских каталогов  
- Настроено bind-монтирование  
- Организовано автоматическое подключение на клиенте  
- Обеспечена безопасность через SELinux и firewall  
- Реализованы автоматические provisioning-скрипты для server и client