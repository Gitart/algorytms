Разбор INI-файла конфигурации с использованием Bash shell-скрипта.

### Образец INI-файла

Я буду использовать следующий файл конфигурации INI в следующих примерах.

[[main]
description = Sample configuration
timeout = 10
monitoring_interval = 20

[database]
server = db.example.org
port = 3306
username = dbuser
password = dbpass

[monitor]
servers[] = www.example.org
servers[] = proxy.example.org
servers[] = cache.example.org
servers[] = bastion.example.org


### Спарсить весь INI-файл

Прочитаем и проанализируем весь INI-файл.

```sh
#!/bin/bash
# Read and parse simple INI file

# Get INI section
ReadINISections(){
  local filename="$1"
  awk '{ if ($1 ~ /^\\\[/) section=tolower(gensub(/\\\[(.+)\\\]/,"\\\\1",1,$1)); configuration\[section\]=1 } END {for (key in configuration) { print key} }' ${filename}
}

# Get/Set all INI sections
GetINISections(){
  local filename="$1"

  sections="$(ReadINISections $filename)"
  for section in $sections; do
    array\_name="configuration\_${section}"
    declare -g -A ${array\_name}
  done
  eval $(awk -F= '{
                    if ($1 ~ /^\\\[/)
                      section=tolower(gensub(/\\\[(.+)\\\]/,"\\\\1",1,$1))
                    else if ($1 !~ /^$/ && $1 !~ /^;/) {
                      gsub(/^\[ \\t\]+|\[ \\t\]+$/, "", $1);
                      gsub(/\[\\\[\\\]\]/, "", $1);
                      gsub(/^\[ \\t\]+|\[ \\t\]+$/, "", $2);
                      if (configuration\[section\]\[$1\] == "")
                        configuration\[section\]\[$1\]=$2
                      else
                        configuration\[section\]\[$1\]=configuration\[section\]\[$1\]" "$2}
                    }
                    END {
                      for (section in configuration)
                        for (key in configuration\[section\])
                          print "configuration\_"section"\[\\""key"\\"\]=\\""configuration\[section\]\[key\]"\\";"
                    }' ${filename}
        )

}

if \[ "$#" -eq "1" \] && \[ -f "$1" \]; then
  filename="$1"
  GetINISections "$filename"

  echo -n "Configuration description: "
  if \[ -n "${configuration\_main\["description"\]}" \]; then
    echo "${configuration\_main\["description"\]}"
  else
    echo "missing"
  fi
  echo

  for section in $(ReadINISections "configuration.ini"); do
    echo "\[${section}\]"
    for key in $(eval echo $\\{'!'configuration\_${section}\[@\]\\}); do
            echo -e "  ${key} = $(eval echo $\\{configuration\_${section}\[$key\]\\}) (access it using $(echo $\\{configuration\_${section}\[$key\]\\}))"
    done
  done
else
  echo "missing INI file"
fi
```

## Вывод будет выглядеть так.

$ parseini configuration.ini
Configuration description: Sample configuration
Configuration description: Sample configuration

[database]
  username = dbuser (access it using ${configuration_database[username]})
  server = db.example.org (access it using ${configuration_database[server]})
  password = dbpass (access it using ${configuration_database[password]})
  port = 3306 (access it using ${configuration_database[port]})
[main]
  description = Sample configuration (access it using ${configuration_main[description]})
  timeout = 10 (access it using ${configuration_main[timeout]})
  monitoring_interval = 20 (access it using ${configuration_main[monitoring_interval]})
[monitor]
  servers = www.example.org proxy.example.org cache.example.org bastion.example.org (access it using ${configuration_monitor[servers]})
  
**Парсинг одного раздела в INI-файле**

Прочитаем и проанализируем отдельный раздел в INI-файле.

```
#!/bin/bash
# Read and parse single section in INI file

# Get/Set single INI section
GetINISection() {
  local filename="$1"
  local section="$2"

  array\_name="configuration\_${section}"
  declare -g -A ${array\_name}
  eval $(awk -v configuration\_array="${array\_name}" \\
             -v members="$section" \\
             -F= '{
                    if ($1 ~ /^\\\[/)
                      section=tolower(gensub(/\\\[(.+)\\\]/,"\\\\1",1,$1))
                    else if ($1 !~ /^$/ && $1 !~ /^;/) {
                      gsub(/^\[ \\t\]+|\[ \\t\]+$/, "", $1);
                      gsub(/\[\\\[\\\]\]/, "", $1);
                      gsub(/^\[ \\t\]+|\[ \\t\]+$/, "", $2);
                      if (section == members) {
                        if (configuration\[section\]\[$1\] == "")
                          configuration\[section\]\[$1\]=$2
                        else
                          configuration\[section\]\[$1\]=configuration\[section\]\[$1\]" "$2}
                      }
                    }
                    END {
                        for (key in configuration\[members\])
                          print configuration\_array"\[\\""key"\\"\]=\\""configuration\[members\]\[key\]"\\";"
                    }' ${filename}
        )
}

if \[ "$#" -eq "2" \] && \[ -f "$1" \] && \[ -n "$2" \]; then
  filename="$1"
  section="$2"
  GetINISection "$filename" "$section"

  echo "\[${section}\]"
  for key in $(eval echo $\\{'!'configuration\_${section}\[@\]\\}); do
          echo -e "  ${key} = $(eval echo $\\{configuration\_${section}\[$key\]\\}) (access it using $(echo $\\{configuration\_${section}\[$key\]\\}))"
  done
else
  echo "missing INI file and/or INI section"
fi
```

Вывод будет выглядеть так.

$ parseinisection configuration.ini main
[main]
  description = Sample configuration (access it using ${configuration_main[description]})
  timeout = 10 (access it using ${configuration_main[timeout]})
  monitoring_interval = 20 (access it using ${configuration_main[monitoring_interval]})
