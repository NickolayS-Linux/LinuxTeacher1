Выполнение ДЗ Занятие 2. Работа с mdadm.

Цель домашнего задания.

Научиться использовать утилиту для управления программными RAID-массивами в Linux;

Запустить ВМ c Ubuntu.

• Добавьте в виртуальную машину несколько дисков

• Соберите RAID-0/1/5/10 на выбор

• Сломайте и почините RAID

• Создайте GPT таблицу, пять разделов и смонтируйте их в системе.

Оформить отчет в README-файле в GitHub-репозитории.

В данном руководстве рассмотрен процесс работы с утилитой mdadm. Функциональные и нефункциональные требования.

ПК на Linux c 16 ГБ ОЗУ или виртуальная машина с системой Ubuntu. 5 дисков, 1 под систему, 2 для выполнения ДЗ

Создал репозиторий на GitHub - https://github.com/

Предварительно установленное и настроенное следующее ПО: Oracle VirtualBox (https://www.virtualbox.org/wiki/Linux_Downloads). Все дальнейшие действия были проверены при использовании VirtualBox 7.2.6 r172322, хостовая ОС: Ubuntu 24.04 Desktop, гостевая система — Ubuntu 24.04.4 LTS.

Добавляю виртуальные диски, скрин ДЗ 1.png

Запуск виртуальной машины.

Запускаю Putty, подключаюсь по ssh к виртуальной машине. логинюсь. Скрин ДЗ 2.png

Получаю информацию о дисках

root@ubuntulinux:~# lsblk . Скрин ДЗ 3.png

Собраю  RADID массив 1, из дисков /dev/md126 из sdb, sdc (Для выполнениея ДЗ) и /dev/md127 из sdd, sde (Дополнительно). Скрин ДЗ 4.png - ДЗ 4-3.png

root@ubuntulinux:~# mdadm --create /dev/md126 -l 1 -n 2 /dev/sdb /dev/sdc

root@ubuntulinux:~# mdadm --create /dev/md127 -l 1 -n 2 /dev/sdd /dev/sde

Создаю файловую систему XFS на обоих дисках, скрин ДЗ 5.png

root@ubuntulinux:~# mkfs.xfs /dev/md127

root@ubuntulinux:~# mkfs.xfs /dev/md127

Сонтирую диски, Срикн ДЗ 5-1.png

root@ubuntulinux:~# mount /dev/md126 /mnt/md126

root@ubuntulinux:~# mount /dev/md127 /mnt/md127

Копируем данные, Скрин ДЗ 5-2.png

root@ubuntulinux:~# cp -r /var/log/* /mnt/md126

Помечаю один диск в массиве md126 как FAULTY, и удаляем диск из массива md126

root@ubuntulinux:~#  mdadm /dev/md126 --fail /dev/sdb, Скрин ДЗ 6.png - ДЗ 6-1.png

Удаляем поломанный диск из RAID, Скрин ДЗ 6-1-1.png

root@ubuntulinux:~# mdadm /dev/md126 --remove /dev/sdb

Проверяю, что данные по прежнему присутсвуют , Скрин ДЗ 6-2.png, ДЗ 6-3.png

root@ubuntulinux:~# du sh /mnt/md126

root@ubuntulinux:~# ls -al /mnt/md126

Поменяли диск, подключаем его к RAID 1 /dev/md126, Скрины ДЗ 7.png, ДЗ 7-1.png, ДЗ 7-2.png
root@ubuntulinux:~#mdadm /dev/md126 -a /dev/sdb
 
RAID 1 массив восстановлен, и готов к работе, данные на месте Скрины ДЗ 7-3.png, ДЗ 7-4.png 

На этом работа с RAID 1 окончена

Следующий этап.  Создам GPT таблицу, пять разделов и смонтируйте их в системе

Содаю диск с GPT, и помечаю его что он с меткой GPT. Скрин ДЗ 8.png

root@ubuntulinux:~# parted /dev/sdd mklabel gpt

Далее создаю разделы в количестве 5 шт. по 10% Скин ДЗ 8-1.png,  ДЗ 8-2.png

root@ubuntulinux:~#parted -a opt /dev/sdd mkpart primary xfs 0% 10%

root@ubuntulinux:~#parted -a opt /dev/sdd mkpart primary xfs 10% 20%

root@ubuntulinux:~#parted -a opt /dev/sdd mkpart primary xfs 20% 30%

root@ubuntulinux:~#parted -a opt /dev/sdd mkpart primary xfs 30% 40%

root@ubuntulinux:~#parted -a opt /dev/sdd mkpart primary xfs 40% 50%

Далее форматирую разделы в файловую систему XFS, скрин ДЗ 8-3.png

root@ubuntulinux:~# mkfs.xfs /dev/sdd1

root@ubuntulinux:~# mkfs.xfs /dev/sdd2

root@ubuntulinux:~# mkfs.xfs /dev/sdd3

root@ubuntulinux:~# mkfs.xfs /dev/sdd4

root@ubuntulinux:~# mkfs.xfs /dev/sdd5

Создаю каталоги для монтирования дисков 

root@ubuntulinux:~# mkdir /mnt/1 && mkdir /mnt/2 && mkdir /mnt/3 && mkdir /mnt/4 && mkdir /mnt/5

Монтирую диски, Скрин ДЗ 8-4.png

root@ubuntulinux:~# mount /dev/sdd2 /mnt/2 && mount /dev/sdd3 /mnt/3 && mount /dev/sdd4 /mnt/4 && mount /dev/sdd5 /mnt/5

Проверяем, что у нас диск /dev/sdd с GPT. Скрин ДЗ 9.png

root@ubuntulinux:~# gdisk -l /dev/sdd

Домашнее задание выполнено.

