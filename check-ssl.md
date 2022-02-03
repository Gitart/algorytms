Содержание
* Как проверить дату истечения срока действия сертификата TLS/SSL из командной строки
* Проверим дату истечения срока действия сертификата SSL или TLS
* Общие сведения о параметрах команды openssl
* Поиск даты истечения срока действия сертификата SSL из файла сертификата, закодированного в формате PEM
Скрипт bash/shell для определения даты истечения срока действия сертификата SSL из самого файла crt и предупреждения системного администратора

## Заключение
Как проверить дату истечения срока действия сертификата TLS/SSL из командной строки
Чтобы проверить дату истечения срока действия SSL-сертификата, мы собираемся использовать клиент командной строки OpenSSL.

Клиент OpenSSL предоставляет множество данных, включая даты действия, даты истечения срока действия, кто выпустил сертификат TLS / SSL и многое другое.

Проверим дату истечения срока действия сертификата SSL или TLS
Откройте приложение Терминал, а затем выполните следующую команду:

$ openssl s_client -servername {SERVER_NAME} -connect {SERVER_NAME}:{PORT} | openssl x509 -noout -dates
$ echo | openssl s_client -servername {SERVER_NAME} -connect {SERVER_NAME}:{PORT} | openssl x509 -noout -dates

Давайте узнаем дату истечения срока для www.itsecforu.ru – введите:
```
DOM="www.itsecforu.ru"
PORT="443"
openssl s_client -servername $DOM -connect $DOM:$PORT \
| openssl x509 -noout -dates

Примеры вывода с указанием дат и другой информации:
depth=2 O = Digital Signature Trust Co., CN = DST Root CA X3
verify return:1
depth=1 C = US, O = Let's Encrypt, CN = Let's Encrypt Authority X3
verify return:1
depth=0 CN = www.itsecforu.ru
verify return:1
notBefore=Sep 29 23:10:07 2020 GMT
notAfter=Dec 28 23:10:07 2020 GMT

## Добавьте команду echo, чтобы не нажимать CTRL + C.
Например:
```
DOM="itsecforu.ru"
PORT="443"
```

## note echo added ##
echo | openssl s_client -servername $DOM -connect $DOM:$PORT \
| openssl x509 -noout -dates

Общие сведения о параметрах команды openssl
s_client: команда s_client реализует общий клиент SSL / TLS, который подключается к удаленному хосту с помощью SSL / TLS.
-servername $DOM: установить для расширения TLS SNI (Server Name Indication) в сообщении ClientHello заданное значение.
-connect $DOM: $PORT: указывает хост ($DOM) и дополнительный порт ($PORT) для подключения.
x509: Запускает утилиту отображения и подписи сертификатов.
-noout: запрещает вывод закодированной версии сертификата.
-dates: выводит дату начала и окончания срока действия сертификата TLS или SSL.
Поиск даты истечения срока действия сертификата SSL из файла сертификата, закодированного в формате PEM

Синтаксис следующий: запросите у файла сертификата, когда истечет срок действия сертификата TLS/SSL.

```
$ openssl x509 -enddate -noout -in {/path/to/my/my.pem}
$ openssl x509 -enddate -noout -in /etc/nginx/ssl/itsecforu.ru.fullchain.cer.ecc
$ openssl x509 -enddate -noout -in /etc/nginx/ssl/itsecforu.ru.fullchain.cer
```
notAfter=Dec 29 23:48:42 2020 GMT
Мы также можем проверить, истекает ли срок действия сертификата в указанные сроки.

Например, узнать, истекает ли срок действия сертификата TLS / SSL в течение следующих 7 дней (604800 секунд):
```
$ openssl x509 -enddate -noout -in my.pem -checkend 604800
```

Проверим, истечет ли срок действия сертификата TLS / SSL в следующие 4 месяца:

openssl x509 -enddate -noout -in my.pem -checkend 10520000
Скрипт bash/shell для определения даты истечения срока действия сертификата SSL из самого файла crt и предупреждения системного администратора

### Вот пример скрипта:

```
#!/bin/bash
# -------------------------------------------------------------------------------
PEM="/etc/nginx/ssl/letsencrypt/itsecforu/itsecforu.ru.fullchain.cer"
 
# 7 денй в секундах 
DAYS="604800" 
 
# Email настройки
_sub="$PEM will expire within $DAYS (7 days)."
_from="system-account@your-dommain"
_to="sysadmin@your-domain"
_openssl="/usr/bin/openssl"
$_openssl x509 -enddate -noout -in "$PEM"  -checkend "$DAYS" | grep -q 'Certificate will expire'
 
# Отправка email
if [ $? -eq 0 ]
then
	echo "${_sub}"
        mail -s "$_sub" -r "$_from" "$_to" <<< "Внимание TLS/SSL сертификат ($PEM) скоро истечет на $HOSTNAME [$(date)]"
        source ~/bin/cli_app.sh
        push_to_mobile "$0" "$_sub. See $_to email for detailed log. -- $HOSTNAME " >/dev/null
fi
```
## Заключение

В этом кратком руководстве вы узнали, как найти дату истечения срока действия сертификата TLS / SSL из файла сертификата, закодированного в PEM.
Сертификаты TLS / SSL с истекшим сроком действия могут вызвать простои и запутать конечных пользователей.

Следовательно, очень важно следить за датой истечения срока действия наших сертификатов TLS / SSL.
