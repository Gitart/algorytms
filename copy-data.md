## Скрипт резервного копирования
Следующий скрипт отражает данные каталоги в BACKUP_FOLDERS на флеш-накопитель, то есть если вы удалите файл в исходном каталоге, он также будет удален на флеш-накопителе, и восстановить эти файлы не удастся.
Но у rsync есть опция –backup-dir для перемещения всех удаленных файлов в исходный каталог.
Это хороший вариант, поскольку зеркальная папка на флеш-накопителе будет обновлена, и все удаленные файлы будут сохранены в другом каталоге.
Я использовал переменную DELFILES для определения папки всех удаленных файлов.
Я также хотел знать, был ли процесс резервного копирования успешным или произошла ошибка.
Скрипт добавляет каждый вывод в LOGFILE, который окружен сообщением о запуске и остановке резервного копирования.

```
#!/bin/bash

DEST_DRIVE="/dev/usbbackup" # Backup will be stored on this device 
DEST_MOUNT="/mnt/flashdrive"    # Mount point of flash drive
BACKUP_FOLDERS=("/path/to/dir1" "/path/to/dir2")    # Backup folders
LOGFILE="/var/log/mybackup.log" # Logfile
DELFILES="$DEST_MOUNT/delted_files" # Path to folder for deleted files
THRESHOLD=10    # keep deleted files for 10 days

# write first parameter $1 to logfile
function write_to_log 
{
  # get current date and time
  bkp_date=$(date +%Y-%m-%d@%H:%M:%S)
  echo -e "$bkp_date : $1" >> $LOGFILE
}

# Append log entry
write_to_log "====== Starting Backup ======"

# test whether symbolic link to flash drive exists
if [ ! -h $DEST_DRIVE ]; then
  write_to_log "[X] Error: Backup drive not available"
  write_to_log "====== Backup finished ======\n\n"
  exit 1
fi

# check whether mount point is in use
if [ ! -z "$(mount -l | grep ${DEST_MOUNT})" ]; then 
  write_to_log "[X] Error: Mount point already in use"
  write_to_log "====== Backup finished ======\n\n"
  exit 1
fi

# Mount flash drive
mount $DEST_DRIVE $DEST_MOUNT
if [ $? -ne 0 ]; then
  write_to_log "[X] Error: Could not mount flash drive"
  write_to_log "====== Backup finished ======\n\n"
  exit 1
fi

# delete all files from $DELFILES older than $THRESHOLD days
if [ -d "${DELFILES}" ]; then
  find $DELFILES -type f -mtime $THRESHOLD -delete >> $LOGFILE
fi

# copy files (create backup)
for f in ${BACKUP_FOLDERS[@]}; do
  # check if given file is directory and readable
  if [ -d $f ] &amp;&amp; [ -r $d ]; then
    rsync -avbuz --delete --backup-dir=$DELFILES $f $DEST_MOUNT >> $LOGFILE
  fi
done

# flush file system buffer
sync

# unmount flash drive
umount $DEST_MOUNT
if [ $? -ne 0 ]; then
  write_to_log "[X] Error: Could not unmount flash drive"
fi

write_to_log "====== Backup finished ======\n\n"
```

## Настройка задания cron
Последний шаг – добавить cronjob для запуска скрипта в данный момент времени.
Демон cron предоставляет четыре каталога cron.hourly, cron.daily, cron.weekly и cron.monthly для запуска скриптов / программ.
Я скопировал скрипт в каталог cron.daily, чтобы ежедневно запускать скрипт.
Убедитесь, что скрипт является исполняемым («chmod u + x backup.sh»), и флеш-устройство подключено.
Вы можете посмотреть время выполнения ежедневных cronjobs на crontab:

$ crontab -l
Вот выдержка из моего вывода:

```
# Run hourly cron jobs at 47 minutes after the hour:
47 * * * * /usr/bin/run-parts /etc/cron.hourly 1> /dev/null
#
# Run daily cron jobs at 4:40 every day:
40 4 * * * /usr/bin/run-parts /etc/cron.daily 1> /dev/null
#
# Run weekly cron jobs at 4:30 on the first day of the week:
30 4 * * 0 /usr/bin/run-parts /etc/cron.weekly 1> /dev/null
#
# Run monthly cron jobs at 4:20 on the first day of the month:
20 4 1 * * /usr/bin/run-parts /etc/cron.monthly 1> /dev/null
В моем случае скрипт резервного копирования будет выполнен в 04:40.
```

Если вы хотите изменить время, вы можете использовать:

# crontab -e
