GRE ТУННЕЛЬ

TUNLOCAL = IP-АДРЕС УСТРОЙСТВА С КОТОРОГО ИДЕТ IPTUNNEL

TUNREMOTE = IP-АДРЕС УСТРОЙСТВО К КОТОРОМУ ИДЕТ IPTUNNEL

HOST = ИНТЕРФЕЙС КОТОРЫЙ ИДЕТ В СТОРОНУ ISP

TUNLOCAL И TUNREMOTE IP-АДРЕСА КОТОРЫЕ ИДУТ С HQ-R И BR-R В ISP

![image](https://github.com/tahehik0/netsys/assets/170648571/33a37728-50f1-4489-8759-f6bfbd5707f5)
![image](https://github.com/tahehik0/netsys/assets/170648571/09678171-0864-45cd-a8c3-44633d544719)



RAID5 МАССИВ

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

4 МОДУЛЬ

Сам по себе гайд сделан на основе ALT linux но на демке будет Debian, команды могут немного отличаться, но там работает TAB чтобы проверить есть ли разница в командах.

1.1/2.1
Это два идентичных задания, в которых нам нужно просто изменить статику на DHCP а так же ползунки DNS и ROUTE которые будут ниже (для Debian)
Что бы изменить статику на DHCP, нужно зайти в настройки сети и выбрать Automatic(DHCP) вместо Manual

1.2
В данном задании мы не блокируем сам вход на сайт, а перенаправляем адресацию на localhost
Для этого нам необходимо войти в терминал/терминальную версию (ctrl+alt+f3), далее ввводим логин и пароль (root P@ssw0rd)
Вводим nano /etc/hosts
Видим что у нас есть 127.0.0.1 localhost , на него мы и будем перенаправлять
Для этого нам необходимо написать 127.0.0.1 vk.com/github.com

1.3
Не выходя из терминала/терминальной версии создаем скрипт
nano /home/debian/script

#!/bin/bash
date
who
ip a
df

Сохраняем файл

Далее выдаем права на запуск скрипти и переходим в папку с скриптом , и запускаем скрипт.
chmod ugo+x ./script  (права на запуск)
cd /home/debian  (переход в папку с скриптом)
./script  (запуск скрипта)

И видим все по порядку, дату, активных пользователей, активные адаптеры и свободное место на диске.

3.1
Для отключения постоянной установки ОС следует в localhost.localdomain в Edit Settings отключить галочку на CD/DVD Drive

3.2
Для установки отключения машины в 18:01 следует все в том же терминале/терминальной версии сделать следующие действия:
cd /home/debian
EDITOR=nano crontab -e  (nano - для Debian, mcedit - для ALT)
Под всеми строками написать
01  18  *  *  *  shutdowrn +0 (Для Debian)          |          01  18  *  *  *  shutdown -h now (Для ALT, но его по идее не будет(на всякий случай))
systemctl restart crontab  (Перезагрузка crontab)

3.3
Для решения данной проблемы необходимо в графическом интерфейсе зайти в Настройки > Users > Other Users > Unlock и вводим пароль администратора > Изменяем пароль

3.4
Подключаем не установочный ISO в Edit Settings

После смены ISO делаем следующие действия в терминале/терминальной версии:
mkdir /mnt/cdrom (создание папки cdrom)
mount /dev/sr0 /mnt/cdrom  (привязка DVD к cdrom)
lsblk  (проверка)

3.5
В терминале/терминальной версии делаем следующие действия:
ls -all /var/  (просматриваем файлы и папки в /var/ для того что бы узнать, есть ли разрешения или нету)
chmod o+w /var/* (выдаем права на изменение всех файлов в папке /var/)



Гатова мы получили 20 баллов.
