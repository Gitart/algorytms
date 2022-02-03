# yum install mcelog -y
Файл Mcelog cron по умолчанию находится здесь /etc/cron.hourly/mcelog.cron

# vi /etc/cron.hourly/mcelog.cron

#!/bin/bash

# do not run if mcelogd is running

service mcelogd status >& /dev/null

[ $? -eq 0 ] && exit 0

# is mcelog supported?

/usr/sbin/mcelog --supported >& /dev/null

if [ $? -eq 1 ]; then

       exit 1;

fi

/usr/sbin/mcelog --ignorenodev --filter >> /var/log/mcelog
После этого вставьте строку ниже в файл hardwareerror.sh.

# touch hardwareerror.sh
# vi hardwareerror.sh

#!/bin/bash

[ $(grep -c "error" /var/log/mcelog) -gt 0 ] && echo "Hardware Error Found $(hostname) @ $(date)" | 

cat /var/log/mcelog | mail -s 'H/w Error' info@itsecforu.ru

exit 0
