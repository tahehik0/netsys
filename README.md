# Гайд 

https://github.com/ItsLiventsev/NetSys_Demo_2024/blob/main/README.md

![image](https://github.com/tahehik0/netsys/assets/170648571/c3788f4e-5962-48d5-a560-99fb8195c6ba)

# Network Adapters

CLI - ISP_CLI ; VM NETWORK

ISP - ISP_HQ-R ; ISP_BR-R ; ISP_CLI ; VM NETWORK

BR-R - ISP_BR-R ; BRANCH ; VM NETWORK

BR-SRV - BRANCH ; VM NETWORK

HQ-R - ISP_HQ-R ; HQ ; HQ-R_AD ; VM NETWORK

HQ-SRV - HQ ; VM NETWORK

HQ-AD - HQ-R_AD ; VM NETWORK

# DNS

Устанавливаем DNS

apt-get dist-upgrade

apt-get install bind

apt-get install bind-utils

Создаем папку

mkdir /etc/bind/zone/master

Копируем файлы

cp /etc/bind/localhost /etc/bind/zone/master/hq.work

cp /etc/bind/localhost /etc/bind/zone/master/branch.work

Заходим в файлы и меняем настройки зон сл. образом:

![image](https://github.com/tahehik0/netsys/assets/170648571/7bf011b8-370f-4988-b8d1-845e7bd38d37)

Создаем файлы для PTR записей

mcedit /etc/bind/zone/0.168.192.in-addr.arpa

mcedit /etc/bind/zone/0.16.172.in-addr.arpa

![image](https://github.com/tahehik0/netsys/assets/170648571/85060784-80a6-4304-b749-bb3b3884b161)

mcedit /etc/bind/options.conf

![image](https://github.com/tahehik0/netsys/assets/170648571/2c25f746-221f-4607-a019-8cca2a016285)

mcedit /etc/bind/local.conf

![image](https://github.com/tahehik0/netsys/assets/170648571/5f4b1a97-af99-4264-a36e-dfd13878a101)

Выдаем права каждой зоне, обратной и прямой

chmod ugo+rwx /etc/bind/zone/0.16.172.in-addr.arpa 

chmod ugo+rwx /etc/bind/zone/0.168.192.in-addr.arpa

chmod ugo+rwx /etc/bind/zone/hq.work (**файл может находится в /etc/bind/zone/master/**)

chmod ugo+rwx /etc/bind/zone/branch.work (**файл может находится в /etc/bind/zone/master/**)

Меняем файл запуска

mcedit /lib/systemd/system/bind.service

![image](https://github.com/tahehik0/netsys/assets/170648571/69769206-225e-461e-bb69-590c9fdb5546)

systemctl enable --now bind

reboot

dig hq-srv.hq.work

![image](https://github.com/tahehik0/netsys/assets/170648571/38ff38c4-cd0a-48e8-af47-59ff0392a1aa)

проверка обратной зоны

пslookup 192.168.0.2

## настройка доступа к серверу с др. устройств

![image](https://github.com/tahehik0/netsys/assets/170648571/0e271e3b-1fb8-4d11-bcff-7fc7cf5bc42e)

# NTP

Меняем временную зону

timedatectl set-timezone Europe/Moscow

Устанавливаем пакет

apt-get install -y chrony

sytemctl enable --now chronyd

Настраиваем файл chrony

mcedit /etc/chrony.conf

![image](https://github.com/tahehik0/netsys/assets/170648571/d4ace5a9-fd34-4cc7-b975-7f8b58382533)

reboot

На всех устройствах устанавливаем chrony и делаем сл. действия

mcedit /etc/chrony.conf

server 192.168.0.1 iburst prefer

Проверяем клиентов 

chronyc clients

![image](https://github.com/tahehik0/netsys/assets/170648571/e5693585-efb6-4bf5-b320-2b6df4d05668)


# GRE ТУННЕЛЬ

TUNLOCAL = IP-АДРЕС УСТРОЙСТВА С КОТОРОГО ИДЕТ IPTUNNEL

TUNREMOTE = IP-АДРЕС УСТРОЙСТВО К КОТОРОМУ ИДЕТ IPTUNNEL

HOST = ИНТЕРФЕЙС КОТОРЫЙ ИДЕТ В СТОРОНУ ISP

TUNLOCAL И TUNREMOTE IP-АДРЕСА КОТОРЫЕ ИДУТ С HQ-R И BR-R В ISP

![image](https://github.com/tahehik0/netsys/assets/170648571/33a37728-50f1-4489-8759-f6bfbd5707f5)
![image](https://github.com/tahehik0/netsys/assets/170648571/09678171-0864-45cd-a8c3-44633d544719)


# RAID5 МАССИВ

ДОБАВЛЯЕМ НА BR-SRV 5 ДИСКОВ ОБЪЕМОМ 1 ГБ
![image](https://github.com/tahehik0/netsys/assets/170648571/37921533-8f8f-44d8-8b5d-f84eb645371e)

ДАЛЕЕ УСТАНАВЛИВАЕМ MDADM

apt-get install mdadm

ПРОВЕРЕРЯЕМ НАЗВАНИЯ ДИСКОВ И РАЗДЕЛОВ

lsblk

ГОТОВИМ НОСИТЕЛИ

mdadm --zero--superblock --force /dev/sd{b,c,d,e,f} (b,c,d,e,f - названия дисков из lsblk)

УДАЛЯЕМ СТАРЫЕ ДАННЫЕ

wipefs --all --force /dev/sd{b,c,d,e,f}

СОЗДАЕМ RAID МАССИВ

mdadm --create --verbose /dev/md0 -1 5 -n 5 /dev/sd{b,c,d,e,f}

ПРОВЕРЯЕМ ЧТО МАССИВ СОЗДАЛСЯ 

lsblk

![image](https://github.com/tahehik0/netsys/assets/170648571/7e5596fb-b359-4dce-8a5e-9d1211cec741)


# ТАБЛИЦА ПРОГРАММ В СООТВЕТСТВИИ С ЗАДАНИЯМИ

![image](https://github.com/tahehik0/netsys/assets/170648571/45a9c72f-6548-4f4e-b9e2-518a2fcb34d2)



# 4 МОДУЛЬ

Сам по себе гайд сделан на основе ALT linux но на демке будет Debian, команды могут немного отличаться, но там работает TAB чтобы проверить есть ли разница в командах.

## 1.1/2.1

Это два идентичных задания, в которых нам нужно просто изменить статику на DHCP а так же ползунки DNS и ROUTE которые будут ниже (для Debian)

Что бы изменить статику на DHCP, нужно зайти в настройки сети и выбрать Automatic(DHCP) вместо Manual

## 1.2

В данном задании мы не блокируем сам вход на сайт, а перенаправляем адресацию на localhost

Для этого нам необходимо войти в терминал/терминальную версию (ctrl+alt+f3), далее ввводим логин и пароль (root P@ssw0rd)

Вводим nano /etc/hosts

Видим что у нас есть 127.0.0.1 localhost , на него мы и будем перенаправлять

Для этого нам необходимо написать 127.0.0.1 vk.com/github.com

## 1.3

Не выходя из терминала/терминальной версии создаем скрипт
nano /home/debian/script

#!/bin/bash

date

who

ip a

df

Сохраняем файл

Далее выдаем права на запуск скрипта и переходим в папку с скриптом , и запускаем скрипт.

chmod ugo+x ./script  (права на запуск)

cd /home/debian  (переход в папку с скриптом)

./script  (запуск скрипта)

И видим все по порядку, дату, активных пользователей, активные адаптеры и свободное место на диске.

## 3.1
Для отключения постоянной установки ОС следует в localhost.localdomain в Edit Settings отключить галочку на CD/DVD Drive

## 3.2
Для установки отключения машины в 18:01 следует все в том же терминале/терминальной версии сделать следующие действия:

cd /home/debian

EDITOR=nano crontab -e  (nano - для Debian, mcedit - для ALT)

Под всеми строками написать

01  18  *  *  *  shutdowrn +0 (Для Debian)          |          01  18  *  *  *  shutdown -h now (Для ALT, но его по идее не будет(на всякий случай))

systemctl restart crontab  (Перезагрузка crontab)

## 3.3
Для решения данной проблемы необходимо в графическом интерфейсе зайти в Настройки > Users > Other Users > Unlock и вводим пароль администратора > Изменяем пароль

## 3.4
Подключаем не установочный ISO в Edit Settings

После смены ISO делаем следующие действия в терминале/терминальной версии:

mkdir /mnt/cdrom (создание папки cdrom)

mount /dev/sr0 /mnt/cdrom  (привязка DVD к cdrom)

lsblk  (проверка)

## 3.5

В терминале/терминальной версии делаем следующие действия:

ls -all /var/  (просматриваем файлы и папки в /var/ для того что бы узнать, есть ли разрешения или нету)

chmod o+w /var/* (выдаем права на изменение всех файлов в папке /var/)

# Настройка SSH на каждом linux устройстве

mcedit /etc/openssh/sshd_config

![image](https://github.com/tahehik0/netsys/assets/170648571/0331fcbc-350d-4914-8295-f289e53598ba)

![image](https://github.com/tahehik0/netsys/assets/170648571/ba9c627c-16c4-42c0-bc48-aa2108ed89f3)

![image](https://github.com/tahehik0/netsys/assets/170648571/a14f629a-bb35-4d04-b823-3d8bf904d4b4)

mcedit /etc/issue.net

