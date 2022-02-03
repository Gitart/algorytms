## Этот скрипт оболочки предназначен для конкретной задачи, необходимой для нашего сервера.

Задача
Наша задача – скопировать все файлы, созданные в каталоге, в другой каталог с такой же структурой каталогов.

Скрипт будет запускаться с регулярным интервалом, искать все файлы, созданные в исходном каталоге, и копировать их в каталог назначения.

Скрипт должен поддерживать ту же структуру каталогов в каталоге назначения.

После этого он изменит права в каталоге назначения.

После успешного копирования файла скрипт должен удалить файлы из исходного каталога.

Shell скрипт резервного копирования Linux
Создайте скрипт оболочки в вашей системе и добавьте содержимое, показанное ниже.

Обновите исходные и целевые каталоги в нужном месте.

После сохраенения не забудьте сделать скрипт исполняемым:

chmod +x script.sh

```
#!/bin/bash

##################### переменные #############################

### Установите правильное местоположение источника и места назначения
SOURCE_DIR="/source/dir/"
DEST_DIR="/desination/dir/"
TMP_FILE=/tmp/copyfileslist.txt

### Установите имя пользователя и имя группы, чтобы установить права на скопированные файлы
### Установите для CHANGE_OWNERSHIP значение 1, чтобы изменить владельца, или 0, чтобы не изменять его.
CHANGE_OWNERSHIP=1
USER='root'
GROUP='root'


########### Не редактируйте, пока не потребуется #################

### Проверка, существует ли исходный каталог
### Скрипт остановится, если источника не существует

if [ -d "${SOURCE_DIR}" ]; then
echo "Source directory found"
else
echo "Source directory not found. Please check above variables are set correctly"
echo "script exited"
exit 1
fi

### Проверка, существует ли каталог назначения
### Скрипт создаст каталог назначения, если он не существует.
### Если не удалось создать каталог, скрипт будет прерван

if [ -d "${DEST_DIR}" ]; then
echo "Destination directory found, all ok"
else
echo "Destination directory not found, creating now"
mkdir -p "${DEST_DIR}"
if [ $? -eq 0 ]; then
echo "Successfully created destination directory."
else
echo "Failed to create destination directory. Script exited"
exit 1
fi
fi


### Копирование всех файлов, доступных в исходном каталоге
### После успешного копирования файла скрипт удалит их из исходного каталога.
cd "${SOURCE_DIR}"

if [ $? -eq 0 ]; then
find . -type f > ${TMP_FILE}

while read CURRENT_FILE_NAME
do
cp --parents "${CURRENT_FILE_NAME}" "${DEST_DIR}"
if [ $? -eq 0 ]; then
echo "File ${CURRENT_FILE_NAME} successfully copied."
rm -f "${CURRENT_FILE_NAME}"
else
echo "File ${CURRENT_FILE_NAME} failed to copy"
fi
done < ${TMP_FILE}
rm -f ${TMP_FILE}
fi

## Установка прав на новые файлы

if [ ${CHANGE_OWNERSHIP} -eq 1 ]; then
sudo chmod 775 -R "${DEST_DIR}"
sudo chown ${USER}:${GROUP} -R "${DEST_DIR}"
fi

################### Конец скрипта ###################################
```
