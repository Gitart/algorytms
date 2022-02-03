# Как сделать ежедневные резервные копии с помощью rsync и cronjobs

![](https://itsecforu.ru/wp-content/uploads/2018/12/backup.jpg) [Мануал](https://itsecforu.ru/category/%d0%bc%d0%b0%d0%bd%d1%83%d0%b0%d0%bb/)

Автор cryptoparty На чтение 6 мин Опубликовано 06.12.2018

Всегда рекомендуется делать резервные копии важных данных (документов, изображений, музыки и т. д.),

Например. худший случай для студентов – потерять данные дипломов или курсовых работ.

Резервная копия должна храниться на флэш-накопителе USB.

Но я также хотел сохранить удаленные файлы на пару дней, в случае случайного удаления файлов.

Есть много способов выполнить эту задачу, но я решил использовать для этой цели rsync и cronjob.

Оба инструмента делают процесс резервного копирования очень гибким.

Очень легко управлять временем процесса резервного копирования cronjobs, а rsync упрощает изменение адресата (например, другой папки, устройства или удаленного хоста).

Моя идея состояла в том, чтобы добавить cronjob, чтобы запустить скрипт bash, который использует rsync для копирования каталогов и файлов на флешку.

Но имена устройств флеш-накопителей меняются, если вы подключаете их в другом порядке.

Чтобы избежать этой проблемы, я добавил правило udev для создания символической ссылки каждый раз, когда флешка подключена.

Содержание

1.  [Добавьте правило udev для создания символической ссылки](https://itsecforu.ru/2018/12/06/%d0%ba%d0%b0%d0%ba-%d1%81%d0%b4%d0%b5%d0%bb%d0%b0%d1%82%d1%8c-%d0%b5%d0%b6%d0%b5%d0%b4%d0%bd%d0%b5%d0%b2%d0%bd%d1%8b%d0%b5-%d1%80%d0%b5%d0%b7%d0%b5%d1%80%d0%b2%d0%bd%d1%8b%d0%b5-%d0%ba%d0%be%d0%bf/#dobavte-pravilo-udev-dlya-sozdaniya-simvolicheskoy)
2.  [Скрипт резервного копирования](https://itsecforu.ru/2018/12/06/%d0%ba%d0%b0%d0%ba-%d1%81%d0%b4%d0%b5%d0%bb%d0%b0%d1%82%d1%8c-%d0%b5%d0%b6%d0%b5%d0%b4%d0%bd%d0%b5%d0%b2%d0%bd%d1%8b%d0%b5-%d1%80%d0%b5%d0%b7%d0%b5%d1%80%d0%b2%d0%bd%d1%8b%d0%b5-%d0%ba%d0%be%d0%bf/#skript-rezervnogo-kopirovaniya)
3.  [Настройка задания cron](https://itsecforu.ru/2018/12/06/%d0%ba%d0%b0%d0%ba-%d1%81%d0%b4%d0%b5%d0%bb%d0%b0%d1%82%d1%8c-%d0%b5%d0%b6%d0%b5%d0%b4%d0%bd%d0%b5%d0%b2%d0%bd%d1%8b%d0%b5-%d1%80%d0%b5%d0%b7%d0%b5%d1%80%d0%b2%d0%bd%d1%8b%d0%b5-%d0%ba%d0%be%d0%bf/#nastroyka-zadaniya-cron)

### Добавьте правило udev для создания символической ссылки

Прежде всего, нужно было выяснить имя устройства флеш-накопителя.

Поэтому я подключил его и посмотрел на разделы:

$ cat /proc/partitions

Эти две строки были наиболее интересными из результатов:

major minor  #blocks  name

\[...\]
   8        0    1007616 sda
   8        1    1007584 sda1

Название моего USB-накопителя на 1 ГБ – /dev/sda1.

Но мне нужно было больше информации (идентификатор продавца, идентификатор продукта и серийный номер) флеш-накопителя, чтобы добавить правило udev. я использовал

$ lsusb -v

чтобы получить подробную информацию обо всех устройствах USB, которые подключены.

Вот выдержка из вывода со всей необходимой мне информацией:

Bus 001 Device 005: ID 03ce:2d4f Kingston Technology Company Inc.
Device Descriptor:
  \[...\]
  idVendor           0x03ce Kingston Technology Company Inc.
  idProduct          0x2d4f DataTraveler
  \[...\]
  iSerial                 3 917C140607D8
  \[...\]

Правила udev находятся в /etc/udev/rules.d и имеют «специальное» имя файла.

Префикс – это число, за которым следует имя, а суффикс – .rules.

Все правила udev обрабатываются в лексическом порядке, т. е. вы можете установить порядок правил udev с префиксом правила.

Я создал файл с именем 80-backup.rules в файле /etc/udev/rules.d.

Содержимое файлов правил udev является ключевыми, парами значений, и для этой цели наиболее важными являются KERNEL и ATTR.

Значение KERNEL сопоставляется с именем устройства ядра, а значения ATTR сопоставляются с атрибутами устройства (я не хочу вдаваться в подробности).

Я использовал “?” чтобы избежать изменения имен устройств флеш-накопителя. Это содержание моего созданного правила udev:

KERNEL=="sd?1", ATTRS{idVendor}=="03ce", ATTRS{idProduct}=="2d4f", \\
ATTRS{serial}=="917C140607D8", SYMLINK+="usbbackup"

Значение SYMLINK usbbackup – это имя символической ссылки, которая создается в /dev каждый раз, когда устройство подключено, т. е. Символическая ссылка автоматически указывает на правильное устройство.

Вам необходимо перезагрузить правила udev или перезапустить демон udev, чтобы «активировать» правило udev.

В моем случае (Slackware Linux) я использовал:

$ /etc/rc.d/rc.udev restart

Теперь правило udev активируется и будет создавать символическую ссылку каждый раз, когда устройство будет подключено.

Обновить:

В Arch Linux вам не нужно перезагружать демон udev, он автоматически обнаруживает изменения.

Просто подключите USB-накопитель снова, и символическая ссылка должна быть доступна.

### Скрипт резервного копирования

Следующий скрипт отражает данные каталоги в BACKUP\_FOLDERS на флеш-накопитель, то есть если вы удалите файл в исходном каталоге, он также будет удален на флеш-накопителе, и восстановить эти файлы не удастся.

Но у rsync есть опция –backup-dir для перемещения всех удаленных файлов в исходный каталог.

Это хороший вариант, поскольку зеркальная папка на флеш-накопителе будет обновлена, и все удаленные файлы будут сохранены в другом каталоге.

Я использовал переменную DELFILES для определения папки всех удаленных файлов.

Я также хотел знать, был ли процесс резервного копирования успешным или произошла ошибка.

Скрипт добавляет каждый вывод в LOGFILE, который окружен сообщением о запуске и остановке резервного копирования.

#!/bin/bash

DEST\_DRIVE="/dev/usbbackup" # Backup will be stored on this device
DEST\_MOUNT="/mnt/flashdrive"    # Mount point of flash drive
BACKUP\_FOLDERS=("/path/to/dir1" "/path/to/dir2")    # Backup folders
LOGFILE="/var/log/mybackup.log" # Logfile
DELFILES="$DEST\_MOUNT/delted\_files" # Path to folder for deleted files
THRESHOLD=10    # keep deleted files for 10 days

# write first parameter $1 to logfile
function write\_to\_log
{
  # get current date and time
  bkp\_date=$(date +%Y-%m-%d@%H:%M:%S)
  echo -e "$bkp\_date : $1" >> $LOGFILE
}

# Append log entry
write\_to\_log "====== Starting Backup ======"

# test whether symbolic link to flash drive exists
if \[ ! -h $DEST\_DRIVE \]; then
  write\_to\_log "\[X\] Error: Backup drive not available"
  write\_to\_log "====== Backup finished ======\\n\\n"
  exit 1
fi

# check whether mount point is in use
if \[ ! -z "$(mount -l | grep ${DEST\_MOUNT})" \]; then
  write\_to\_log "\[X\] Error: Mount point already in use"
  write\_to\_log "====== Backup finished ======\\n\\n"
  exit 1
fi

# Mount flash drive
mount $DEST\_DRIVE $DEST\_MOUNT
if \[ $? -ne 0 \]; then
  write\_to\_log "\[X\] Error: Could not mount flash drive"
  write\_to\_log "====== Backup finished ======\\n\\n"
  exit 1
fi

# delete all files from $DELFILES older than $THRESHOLD days
if \[ -d "${DELFILES}" \]; then
  find $DELFILES -type f -mtime $THRESHOLD -delete >> $LOGFILE
fi

# copy files (create backup)
for f in ${BACKUP\_FOLDERS\[@\]}; do
  # check if given file is directory and readable
  if \[ -d $f \] &amp;&amp; \[ -r $d \]; then
    rsync -avbuz --delete --backup-dir=$DELFILES $f $DEST\_MOUNT >> $LOGFILE
  fi
done

# flush file system buffer
sync

# unmount flash drive
umount $DEST\_MOUNT
if \[ $? -ne 0 \]; then
  write\_to\_log "\[X\] Error: Could not unmount flash drive"
fi

write\_to\_log "====== Backup finished ======\\n\\n"

### Настройка задания cron

Последний шаг – добавить cronjob для запуска скрипта в данный момент времени.

Демон cron предоставляет четыре каталога cron.hourly, cron.daily, cron.weekly и cron.monthly для запуска скриптов / программ.

Я скопировал скрипт в каталог cron.daily, чтобы ежедневно запускать скрипт.

Убедитесь, что скрипт является исполняемым («chmod u + x backup.sh»), и флеш-устройство подключено.

Вы можете посмотреть время выполнения ежедневных cronjobs на crontab:

$ crontab -l

Вот выдержка из моего вывода:

\# Run hourly cron jobs at 47 minutes after the hour:
47 \* \* \* \* /usr/bin/run-parts /etc/cron.hourly 1> /dev/null
#
# Run daily cron jobs at 4:40 every day:
40 4 \* \* \* /usr/bin/run-parts /etc/cron.daily 1> /dev/null
#
# Run weekly cron jobs at 4:30 on the first day of the week:
30 4 \* \* 0 /usr/bin/run-parts /etc/cron.weekly 1> /dev/null
#
# Run monthly cron jobs at 4:20 on the first day of the month:
20 4 1 \* \* /usr/bin/run-parts /etc/cron.monthly 1> /dev/null

В моем случае скрипт резервного копирования будет выполнен в 04:40.

Если вы хотите изменить время, вы можете использовать:

\# crontab -e
