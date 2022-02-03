# Как контролировать веб-сервер Apache с помощью Prometheus и Grafana за 5 минут

![](https://itsecforu.ru/wp-content/uploads/2018/11/apache-prometheus-metrics-grafana-min-1.png) [Мануал](https://itsecforu.ru/category/%d0%bc%d0%b0%d0%bd%d1%83%d0%b0%d0%bb/)

Автор cryptoparty На чтение 5 мин Опубликовано 22.11.2018

Добро пожаловать в наш справочник о том, как контролировать веб-сервер Apache с помощью Prometheus и Grafana менее чем за 5 минут.

Эта настройка должна работать для любой версии веб-сервера Apache, работающего под любым Linux.

Содержание

1.  [Шаг 1: Загрузите и установите Apache Prometheus exporter](https://itsecforu.ru/2018/11/22/%d0%ba%d0%b0%d0%ba-%d0%ba%d0%be%d0%bd%d1%82%d1%80%d0%be%d0%bb%d0%b8%d1%80%d0%be%d0%b2%d0%b0%d1%82%d1%8c-%d0%b2%d0%b5%d0%b1-%d1%81%d0%b5%d1%80%d0%b2%d0%b5%d1%80-apache-%d1%81-%d0%bf%d0%be%d0%bc%d0%be/#shag-1-zagruzite-i-ustanovite-apache-prometheus)
2.  [Шаг 2: Создайте службу systemd Apache Prometheus exporter](https://itsecforu.ru/2018/11/22/%d0%ba%d0%b0%d0%ba-%d0%ba%d0%be%d0%bd%d1%82%d1%80%d0%be%d0%bb%d0%b8%d1%80%d0%be%d0%b2%d0%b0%d1%82%d1%8c-%d0%b2%d0%b5%d0%b1-%d1%81%d0%b5%d1%80%d0%b2%d0%b5%d1%80-apache-%d1%81-%d0%bf%d0%be%d0%bc%d0%be/#shag-2-sozdayte-sluzhbu-systemd-apache-prometheus)
3.  [Шаг 3. Запустите Apache Prometheus и включите службу для запуска при загрузке системы.](https://itsecforu.ru/2018/11/22/%d0%ba%d0%b0%d0%ba-%d0%ba%d0%be%d0%bd%d1%82%d1%80%d0%be%d0%bb%d0%b8%d1%80%d0%be%d0%b2%d0%b0%d1%82%d1%8c-%d0%b2%d0%b5%d0%b1-%d1%81%d0%b5%d1%80%d0%b2%d0%b5%d1%80-apache-%d1%81-%d0%bf%d0%be%d0%bc%d0%be/#shag-3-zapustite-apache-prometheus-i-vklyuchite)
4.  [Шаг 4: Добавить exporter задачу](https://itsecforu.ru/2018/11/22/%d0%ba%d0%b0%d0%ba-%d0%ba%d0%be%d0%bd%d1%82%d1%80%d0%be%d0%bb%d0%b8%d1%80%d0%be%d0%b2%d0%b0%d1%82%d1%8c-%d0%b2%d0%b5%d0%b1-%d1%81%d0%b5%d1%80%d0%b2%d0%b5%d1%80-apache-%d1%81-%d0%bf%d0%be%d0%bc%d0%be/#shag-4-dobavit-exporter-zadachu)
5.  [Шаг 5: добавьте Dashboard в Grafana](https://itsecforu.ru/2018/11/22/%d0%ba%d0%b0%d0%ba-%d0%ba%d0%be%d0%bd%d1%82%d1%80%d0%be%d0%bb%d0%b8%d1%80%d0%be%d0%b2%d0%b0%d1%82%d1%8c-%d0%b2%d0%b5%d0%b1-%d1%81%d0%b5%d1%80%d0%b2%d0%b5%d1%80-apache-%d1%81-%d0%bf%d0%be%d0%bc%d0%be/#shag-5-dobavte-dashboard-v-grafana)

### Шаг 1: Загрузите и установите Apache Prometheus exporter

export VER="0.5.0"
wget [https://github.com/Lusitaniae/apache\_exporter/releases/download/v${VER}/apache\_exporter-${VER}.linux-amd64.tar.gz](https://github.com/Lusitaniae/apache_exporter/releases/download/v$%7BVER%7D/apache_exporter-$%7BVER%7D.linux-amd64.tar.gz)

Распакуйте полученный архив

tar xvf apache\_exporter-${VER}.linux-amd64.tar.gz
sudo cp apache\_exporter-${VER}.linux-amd64/apache\_exporter /usr/local/bin

**apache\_exporter** должен быть исполняемым из вашего текущего SHELL

$ **apache\_exporter -version**
apache\_exporter, version 0.5.0 (branch: HEAD, revision: f6a5b4814ea795ee9eac745c55649cce9e5117a9)
  build user:       root@0fdc4d8924f5
  build date:       20171113-21:19:13
  go version:       go1.9.2

### Шаг 2: Создайте службу systemd Apache Prometheus exporter

Во-первых, добавьте пользователя prometheus, который будет запускать службу

sudo groupadd --system prometheus
sudo useradd -s /sbin/nologin --system -g prometheus prometheus

Затем перейдите к созданию файла служебной единицы systemd.

sudo vim /etc/systemd/system/apache\_exporter.service

Добавьте содержимое ниже:

\[Unit\]
Description=Prometheus
Documentation=[https://github.com/Lusitaniae/apache\_exporter](https://github.com/Lusitaniae/apache_exporter)
Wants=network-online.target
After=network-online.target

\[Service\]
Type=simple
User=prometheus
Group=prometheus
ExecReload=/bin/kill -HUP $MAINPID
ExecStart=/usr/local/bin/apache\_exporter \\
  --insecure \\
  --scrape\_uri=[http://localhost/server-status/?auto](http://localhost/server-status/?auto) \\
  --telemetry.address=0.0.0.0:9117 \\
  --telemetry.endpoint=/metrics

SyslogIdentifier=apache\_exporter
Restart=always

\[Install\]
WantedBy=multi-user.target

Служба будет прослушивать порт 9117, а показатели будут отображаться в URI/metrics.

Если показатели Apache не указаны в http://localhost/server-status/?auto, вам нужно будет изменить URL-адрес.

Для системы Init, такой как CentOS 6.x, создайте скрипт инициализации в /etc/init.d/

sudo vim /etc/init.d/apache\_exporter

содержимое:

#!/bin/bash
# Author: Josphat Mutai, kiplangatmtai@gmail.com , [https://github.com/jmutai](https://github.com/jmutai)
# apache\_exporter     This shell script takes care of starting and stopping Prometheus apache exporter
#
# chkconfig: 2345 80 80
# description: Prometheus apache exporter  start script
# processname: apache\_exporter
# pidfile: /var/run/apache\_exporter.pid

# Source function library.
. /etc/rc.d/init.d/functions

RETVAL=0
PROGNAME=apache\_exporter
PROG=/usr/local/bin/${PROGNAME}
RUNAS=prometheus
LOCKFILE=/var/lock/subsys/${PROGNAME}
PIDFILE=/var/run/${PROGNAME}.pid
LOGFILE=/var/log/${PROGNAME}.log
DAEMON\_SYSCONFIG=/etc/sysconfig/${PROGNAME}

# GO CPU core Limit

#GOMAXPROCS=$(grep -c ^processor /proc/cpuinfo)
GOMAXPROCS=1

# Source config

. ${DAEMON\_SYSCONFIG}

start() {
    if \[\[ -f $PIDFILE \]\] > /dev/null; then
        echo "apache\_exporter  is already running"
        exit 0
    fi

    echo -n "Starting apache\_exporter  service…"
    daemonize -u ${USER} -p ${PIDFILE} -l ${LOCKFILE} -a -e ${LOGFILE} -o ${LOGFILE} ${PROG} ${ARGS}
    RETVAL=$?
    echo ""
    return $RETVAL
}

stop() {
    if \[ ! -f "$PIDFILE" \] || ! kill -0 $(cat "$PIDFILE"); then
        echo "Service not running"
        return 1
    fi
    echo 'Stopping service…'
    #kill -15 $(cat "$PIDFILE") && rm -f "$PIDFILE"
    killproc -p ${PIDFILE} -d 10 ${PROG}
    RETVAL=$?
    echo
    \[ $RETVAL = 0 \] && rm -f ${LOCKFILE} ${PIDFILE}
    return $RETVAL
}

status() {
    if \[ -f "$PIDFILE" \] || kill -0 $(cat "$PIDFILE"); then
      echo "apache exporter  service running..."
      echo "Service PID: \`cat $PIDFILE\`"
    else
      echo "Service not running"
    fi
     RETVAL=$?
     return $RETVAL
}

# Call function
case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    restart)
        stop
        start
        ;;
    status)
        status
        ;;
    \*)
        echo "Usage: $0 {start|stop|restart}"
        exit 2
esac

Установите пакет daemonize.

sudo yum -y install daemonize

Создать файл конфигурации Arguments

sudo vim /etc/sysconfig/apache\_exporter

Добавьте

ARGS="--insecure --scrape\_uri=[http://localhost/server-status/?auto](http://localhost/server-status/?auto) --telemetry.address=0.0.0.0:9117 --telemetry.endpoint=/metrics"

Протестируйте скрипт

\# /**etc/init.d/apache\_exporter**
Usage: /etc/init.d/apache\_exporter {start|stop|restart}

### Шаг 3. Запустите Apache Prometheus и включите службу для запуска при загрузке системы.

sudo /etc/init.d/apache\_exporter start
sudo chkconfig apache\_exporter on

Вы можете проверить его использование:

$ **sudo /etc/init.d/apache\_exporter status**
apache exporter  service running...
Service PID: 1970

$ **sudo chkconfig --list | grep apache\_exporter**
apache\_exporter 0:off   1:off   2:on    3:on    4:on    5:on    6:off

$ **sudo ss -tunelp | grep 9117**
tcp    LISTEN     0      128                   :::9117                 :::\*      users:(("apache\_exporter",1970,6)) ino:1823474168 sk:ffff880341cd7800

### Шаг 4: Добавить exporter задачу

Добавьте job на сервер Prometheus для определения метрик.

Измените /etc/prometheus/prometheus.yml

\# Apache Servers
  - job\_name: apache1
    static\_configs:
      - targets: \['10.1.10.15:9117'\]
        labels:
          alias: server1-apache

  - job\_name: apache2
    static\_configs:
      - targets: \['10.1.10.16:9117'\]
        labels:
          alias: server2-apache

Перезапустите prometheus

sudo systemctl restart prometheus

Проверьте доступ к порту 9117 с сервера Prometheus

$ **telnet 10.1.10.15 9117**
Trying 10.1.10.15...
Connected to 10.1.10.15.
Escape character is '^\]'.
^\]

### Шаг 5: добавьте Dashboard в Grafana

Последний шаг – создать свою собственную панель для визуализации метрик Apache.

Для этой демонстрации мы будем использовать [Grafana Dashboards by Ricardo F](https://github.com/rfrail3/grafana-dashboards "https://github.com/rfrail3/grafana-dashboards").

У вас должен быть исходник данных Prometheus, уже добавленный в Grafana, или используйте ссылку [Add Prometheus data source](https://prometheus.io/docs/visualization/grafana/), чтобы добавить его.

После того как источник данных был добавлен, импортируйте панель Apache Grafana Dashboard, перейдя в **Dashboard > Import**.

Используйте 3894 для идентификатора панели Grafana.

![](https://itsecforu.ru/wp-content/uploads/2018/11/import-apache-prometheus-dashboard-min-300x140.png)

Дайте ему описательное имя и выберите ранее добавленный источник данных Prometheus.

![](https://itsecforu.ru/wp-content/uploads/2018/11/import-apache-prometheus-dashboard-02-min-300x116.png)

Нажмите кнопку «Import», чтобы начать использовать панель.

Через несколько минут показатели отобразятся.

![](https://itsecforu.ru/wp-content/uploads/2018/11/apache-prometheus-metrics-grafana-min-1-300x127.png)

Выберите другой узел, чтобы показать показатели для использования раскрывающегося меню в верхней части панели показателей.

В следующем руководстве по мониторингу Apache я расскажу об использовании InfluxDB и Grafana для мониторинга веб-сервера Apache.
