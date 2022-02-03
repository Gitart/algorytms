Создайте скрипт оболочки, чтобы определить, как долго указанный удаленный сервер или устройство находились в автономном режиме.

Он не предназначен для замены решения для мониторинга, но для определения того, сколько времени потребуется для перезагрузки сервера или устройства.

Он использует преобразование секунд в читаемый человеком временную метку для отображения результатов.

#!/bin/bash
# Determine how long remote server was offline

# initial sleep time
sleep\_time=1

# ping timeout
timeout=5

if \[ "$#" -ne "1" \]; then
  echo "Provide server/device address as a parameter"
  exit 1
fi

# server/device address is provided as a first parameter
address="$1"

# function to pretty print time
function pretty\_time() {
  # calculate seconds for each time period
  seconds\_in\_year=$(  echo "(365.2425  \* 24 \* 60 \* 60)/1" | bc)
  seconds\_in\_month=$( echo "(30.436875 \* 24 \* 60 \* 60)/1" | bc)
  seconds\_in\_day=$(   echo "(            24 \* 60 \* 60)/1" | bc)
  seconds\_in\_hour=$(  echo "(                 60 \* 60)/1" | bc)
  seconds\_in\_minute=60

  seconds\_param="$1"

  time\_string=""

  # take care of years
  if \[ "$seconds\_param" -ge "$seconds\_in\_year" \]; then
    years=$(expr $seconds\_param \\/ $seconds\_in\_year)
    seconds\_param=$(expr $seconds\_param - $years \\\* $seconds\_in\_year)
    if \[ "$years" -gt "1" \]; then
      time\_string="$years years"
    elif \[ "$years" -eq "1" \]; then
      time\_string="$years year"
    fi
  fi
  # take care of months
  if \[ "$seconds\_param" -ge "$seconds\_in\_month" \]; then
    months=$(expr $seconds\_param \\/ $seconds\_in\_month)
    seconds\_param=$(expr $seconds\_param - $months \\\* $seconds\_in\_month)
    if \[ "$months" -gt "1" \]; then
      time\_string="$time\_string $months months"
    elif \[ "$months" -eq "1" \]; then
      time\_string="$time\_string $months month"
    fi
  fi

  # take care of days
  if \[ "$seconds\_param" -ge "$seconds\_in\_day" \]; then
    days=$(expr $seconds\_param \\/ $seconds\_in\_day)
    seconds\_param=$(expr $seconds\_param - $days \\\* $seconds\_in\_day)
    if \[ "$days" -gt "1" \]; then
      time\_string="$time\_string $days days"
    elif \[ "$days" -eq "1" \]; then
      time\_string="$time\_string $days day"
    fi
  fi

  # take care of hours
  if \[ "$seconds\_param" -ge "$seconds\_in\_hour" \]; then
    hours=$(expr $seconds\_param \\/ $seconds\_in\_hour)
    seconds\_param=$(expr $seconds\_param - $hours \\\* $seconds\_in\_hour)
    if \[ "$hours" -gt "1" \]; then
      time\_string="$time\_string $hours hours"
    elif \[ "$hours" -eq "1" \]; then
      time\_string="$time\_string $hours hour"
    fi
  fi

  # take care of minutes
  if \[ "$seconds\_param" -ge "$seconds\_in\_minute" \]; then
    minutes=$(expr $seconds\_param \\/ $seconds\_in\_minute)
    seconds\_param=$(expr $seconds\_param - $minutes \\\* $seconds\_in\_minute)
    if \[ "$minutes" -gt "1" \]; then
      time\_string="$time\_string $minutes minutes"
    elif \[ "$minutes" -eq "1" \]; then
      time\_string="$time\_string $minutes minute"
    fi
  fi

  # take care of seconds
  seconds=$seconds\_param
  if \[ "$seconds" -gt "1" \]; then
      time\_string="$time\_string $seconds seconds"
  elif \[ "$seconds" -eq "1" \]; then
      time\_string="$time\_string $seconds second"
  fi

  echo "$time\_string" | sed "s/^ //"
}

# initial state
state=0

# server/device was offline when script started
ping -c 1 -W $timeout -q $address 2>&1 1>/dev/null
exit\_code="$?"
if \[ "$exit\_code" -gt "0" \]; then
  state\_in="$(date +%s)"
  state=2
  echo "Server/device \[${address}\] was not responding at \[$(date --date="@${state\_in}" -R)\] when script was executed"
else
  echo "Server/device \[${address}\] was responding at \[$(date -R)\] when script was executed"
fi

while true; do
  ping -c 1 -W $timeout -q $address 2>&1 1>/dev/null
  exit\_code="$?"

  # server/device was offline when script started, but it is online now
  if \[ "$exit\_code" -eq "0" \] && \[ "$state" -eq "2" \]; then
    state\_out="$(date +%s)"
    state\_diff="$(expr $state\_out  - $state\_in)"
    state=0
    echo "Server/device \[${address}\] started responding at \[$(date --date="@${state\_in}" -R)\]"
    echo "Server/device \[${address}\] was offline for at least \[$(pretty\_time $state\_diff)\]"
  # server/device was online, but it is offline now
  elif \[ "$exit\_code" -gt "0" \] && \[ "$state" -eq "0" \]; then
    state\_in="$(date +%s)"
    state=1
    echo "Server/device \[${address}\] stopped responding at \[$(date --date="@${state\_in}" -R)\]"
  # server/device was offline, but it is online now
  elif \[ "$exit\_code" -eq "0" \] && \[ "$state" -eq "1" \]; then
    state\_out="$(date +%s)"
    state\_diff="$(expr $state\_out  - $state\_in)"
    state=0;
    echo "Server/device \[${address}\] started responding at \[$(date --date="@${state\_out}" -R)\]"
    echo "Server/device \[${address}\] was offline for \[$(pretty\_time $state\_diff)\]"
  fi
  sleep $sleep\_time
done

Пример вывода, когда сервер отвечал во время запуска скрипта оболочки.

$ bash offline.sh 192.0.2.11

Server/device \[192.0.2.11\] was responding at \[Fri, 06 Apr 2018 19:37:49 +0000\] when script was executed
Server/device \[192.0.2.11\] stopped responding at \[Fri, 06 Apr 2018 19:38:08 +0000\]
Server/device \[192.0.2.11\] started responding at \[Fri, 06 Apr 2018 19:50:22 +0000\]
Server/device \[192.0.2.11\] was offline for \[12 minutes 14 seconds\] seconds
Server/device \[192.0.2.11\] stopped responding at \[Fri, 06 Apr 2018 19:53:46 +0000\]
Server/device \[192.0.2.11\] started responding at \[Fri, 06 Apr 2018 19:55:59 +0000\]
Server/device \[192.0.2.11\] was offline for \[2 minutes 13 seconds\] seconds

Пример вывода, когда сервер не отвечал во время запуска скрипта оболочки.

$ bash offline.sh 192.0.2.11

Server/device \[192.0.2.11\] was not responding at \[Fri, 06 Apr 2018 19:38:32 +0000\] when script was executed
Server/device \[192.0.2.11\] started responding at \[Fri, 06 Apr 2018 19:38:32 +0000\]
Server/device \[192.0.2.11\] was offline for at least \[11 minutes 48 seconds\] seconds
Server/device \[192.0.2.11\] stopped responding at \[Fri, 06 Apr 2018 19:53:45 +0000\]
Server/device \[192.0.2.11\] started responding at \[Fri, 06 Apr 2018 19:55:58 +0000\]
Server/device \[192.0.2.11\] was offline for \[2 minutes 13 seconds\] seconds
