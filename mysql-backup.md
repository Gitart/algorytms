# Автоматическое резервное копирование нескольких баз данных MySQL или MariaDB

![](https://itsecforu.ru/wp-content/uploads/2018/09/1-5.jpg) [Мануал](https://itsecforu.ru/category/%d0%bc%d0%b0%d0%bd%d1%83%d0%b0%d0%bb/)

## Содержание

1.  [Описание](https://itsecforu.ru/2018/09/06/%d0%b0%d0%b2%d1%82%d0%be%d0%bc%d0%b0%d1%82%d0%b8%d1%87%d0%b5%d1%81%d0%ba%d0%be%d0%b5-%d1%80%d0%b5%d0%b7%d0%b5%d1%80%d0%b2%d0%bd%d0%be%d0%b5-%d0%ba%d0%be%d0%bf%d0%b8%d1%80%d0%be%d0%b2%d0%b0%d0%bd%d0%b8/#opisanie)
2.  [Резервное копирование базы данных](https://itsecforu.ru/2018/09/06/%d0%b0%d0%b2%d1%82%d0%be%d0%bc%d0%b0%d1%82%d0%b8%d1%87%d0%b5%d1%81%d0%ba%d0%be%d0%b5-%d1%80%d0%b5%d0%b7%d0%b5%d1%80%d0%b2%d0%bd%d0%be%d0%b5-%d0%ba%d0%be%d0%bf%d0%b8%d1%80%d0%be%d0%b2%d0%b0%d0%bd%d0%b8/#rezervnoe-kopirovanie-bazy-dannyh)
3.  [Настройка Cronjob](https://itsecforu.ru/2018/09/06/%d0%b0%d0%b2%d1%82%d0%be%d0%bc%d0%b0%d1%82%d0%b8%d1%87%d0%b5%d1%81%d0%ba%d0%be%d0%b5-%d1%80%d0%b5%d0%b7%d0%b5%d1%80%d0%b2%d0%bd%d0%be%d0%b5-%d0%ba%d0%be%d0%bf%d0%b8%d1%80%d0%be%d0%b2%d0%b0%d0%bd%d0%b8/#nastroyka-cronjob)
4.  [Откройте crontab](https://itsecforu.ru/2018/09/06/%d0%b0%d0%b2%d1%82%d0%be%d0%bc%d0%b0%d1%82%d0%b8%d1%87%d0%b5%d1%81%d0%ba%d0%be%d0%b5-%d1%80%d0%b5%d0%b7%d0%b5%d1%80%d0%b2%d0%bd%d0%be%d0%b5-%d0%ba%d0%be%d0%bf%d0%b8%d1%80%d0%be%d0%b2%d0%b0%d0%bd%d0%b8/#otkroyte-crontab)

### Описание

В этой статье мы рассмотрим, как создавать резервные копии нескольких баз данных MySQL или MariaDB, которые размещаются на одном компьютере с использованием пользовательского скрипта bash и создания задания cron.

### Резервное копирование базы данных

У нас есть три базы данных MySQL на нашем Linux-сервере с именем db-hackthesec-site, db-hackthesec-blog, db-hackthesec-app.
Войдите в свою базу данных MySQL или MariaDB и выполните запрос, чтобы создать пользователя базы данных db\_user\_backups для обработки резервных копий.3

```
GRANT LOCK TABLES, SELECT ON \*.\*, SHOW VIEW ON \*.\*, REPLICATION CLIENT ON \*.\* TO 'db\_user\_backups'@'%' IDENTIFIED BY '{COMPLEX-PASSWORD}';
```

Также выполните команду ниже, чтобы убедиться, что MySQL настроен на правильное восстановление хранимых процедур

```
SET GLOBAL log\_bin\_trust\_function\_creators = 1;
```

Настройка необходимой структуры каталогов и необходимых файлов:
```
\# create backup directory with environment and log file
sudo mkdir /backups && cd /backups
sudo touch .env db-backup.sh db-backup.log
sudo chmod -R 775 /backups
sudo chmod -R g+s /backups
sudo chmod +x db-backup.sh

# add mysql backup user credentials into environment file
echo "export MYSQL\_USER=db\_user\_backups" > /backups/.env
echo "export MYSQL\_PASS={COMPLEX-PASSWORD}" >> /backups/.env
```

Откройте db-backup.sh с помощтю nano /backups/db-backup.sh и вставьте код в него, затем сохраните файл (Ctrl + X -> Y -> нажмите Enter).

```
DB\_NAMES=( 'db-hackthesec-site' 'db-hackthesec-blog' 'db-hackthesec-app' ) #replace with your own database name(s)
BKUP\_NAMES=()
BKUP\_DIR="/backups"

# get total number of directories
total\_dbs=${#DB\_NAMES\[@\]}

# create backup file names
for (( i=0; i<${total\_dbs}; i++ )); do
    BKUP\_NAMES\[$i\]="\`date +%Y%m%d%H%M\`-backup-$${DB\_NAMES\[$i\]}.sql.gz"
done

# get backup users credentials
source $BKUP\_DIR/.env

# create backups
for (( i=0; i<${total\_dbs}; i++ )); do
    # NOTE: --routines flag makes sure stored procedures are also backed up
    mysqldump --routines -u ${MYSQL\_USER} -p${MYSQL\_PASS} | gzip > ${BKUP\_DIR}/${BKUP\_NAMES\[$i\]}
done
```

### Настройка Cronjob
#### Откройте crontab

```
crontab -e
```

## Добавьте запись в crontab

```
0 0 \* \* \* /usr/bin/env bash /backups/db-backup.sh &>> /backups/db-backup.log
```

> Примечание. Во время тестирования вы можете установить cronjob для запуска каждую 1 минуту, а не как показано ниже

```
\* \* \* \* \* /usr/bin/env bash /backups/db-backup.sh &>> /backups/db-backup.log
```

\-или- каждые 5 минут (замените 5 на количество минут, которое вы хотите)

```
\*/5 \* \* \* \* /usr/bin/env bash /backups/db-backup.sh &>> /backups/db-backup.log
```
