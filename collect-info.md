# Как выполнить сбор информации на Linux с помощью Digger

![](https://itsecforu.ru/wp-content/uploads/2018/12/digger-linux.jpg) [Мануал](https://itsecforu.ru/category/%d0%bc%d0%b0%d0%bd%d1%83%d0%b0%d0%bb/)

Автор cryptoparty На чтение 4 мин Опубликовано 05.12.2018

Являетесь ли вы тестировщиком на проникновение, пытающимся выполнить быстрый сбор информации ?

Digger – это многофункциональный инструмент, написанный на python для всех ваших первичных сборов данных.

Digger поможет вам выполнить множество основных задач сбора информации.

Он использует API-интерфейсы для сбора всех данных, чтобы скрыть вашу личность.

В этом руководстве я покажу вам, как вы можете установить и использовать Digger для выполнения следующих задач:

*   Поиск Whois
*   Обратный поиск DNS
*   Поиск DNS
*   Сканирование портов
*   Проверка заголовка HTTP
*   Traceroute онлайн
*   Поиск местоположения IP-адресов
*   Проверка robots.txt
*   Извлечение URL

Содержание

1.  [Как скачать инструмент сбора информации Digger](https://itsecforu.ru/2018/12/05/%d0%ba%d0%b0%d0%ba-%d0%b2%d1%8b%d0%bf%d0%be%d0%bb%d0%bd%d0%b8%d1%82%d1%8c-%d1%81%d0%b1%d0%be%d1%80-%d0%b8%d0%bd%d1%84%d0%be%d1%80%d0%bc%d0%b0%d1%86%d0%b8%d0%b8-%d0%bd%d0%b0-linux-%d1%81-%d0%bf%d0%be/#kak-skachat-instrument-sbora-informatsii)
2.  [Как использовать инструмент сбора информации Digger](https://itsecforu.ru/2018/12/05/%d0%ba%d0%b0%d0%ba-%d0%b2%d1%8b%d0%bf%d0%be%d0%bb%d0%bd%d0%b8%d1%82%d1%8c-%d1%81%d0%b1%d0%be%d1%80-%d0%b8%d0%bd%d1%84%d0%be%d1%80%d0%bc%d0%b0%d1%86%d0%b8%d0%b8-%d0%bd%d0%b0-linux-%d1%81-%d0%bf%d0%be/#kak-ispolzovat-instrument-sbora-informatsii)
3.  [Установка зависимостей Python](https://itsecforu.ru/2018/12/05/%d0%ba%d0%b0%d0%ba-%d0%b2%d1%8b%d0%bf%d0%be%d0%bb%d0%bd%d0%b8%d1%82%d1%8c-%d1%81%d0%b1%d0%be%d1%80-%d0%b8%d0%bd%d1%84%d0%be%d1%80%d0%bc%d0%b0%d1%86%d0%b8%d0%b8-%d0%bd%d0%b0-linux-%d1%81-%d0%bf%d0%be/#ustanovka-zavisimostey-python)
4.  [Выполните поиск Whois](https://itsecforu.ru/2018/12/05/%d0%ba%d0%b0%d0%ba-%d0%b2%d1%8b%d0%bf%d0%be%d0%bb%d0%bd%d0%b8%d1%82%d1%8c-%d1%81%d0%b1%d0%be%d1%80-%d0%b8%d0%bd%d1%84%d0%be%d1%80%d0%bc%d0%b0%d1%86%d0%b8%d0%b8-%d0%bd%d0%b0-linux-%d1%81-%d0%bf%d0%be/#vypolnite-poisk-whois)
5.  [Выполнение поиска DNS](https://itsecforu.ru/2018/12/05/%d0%ba%d0%b0%d0%ba-%d0%b2%d1%8b%d0%bf%d0%be%d0%bb%d0%bd%d0%b8%d1%82%d1%8c-%d1%81%d0%b1%d0%be%d1%80-%d0%b8%d0%bd%d1%84%d0%be%d1%80%d0%bc%d0%b0%d1%86%d0%b8%d0%b8-%d0%bd%d0%b0-linux-%d1%81-%d0%bf%d0%be/#vypolnenie-poiska-dns)

### Как скачать инструмент сбора информации Digger

Digger – это скрипт Python, который можно легко загрузить в любой Linux-системе с помощью git clone

$ **git clone [https://github.com/Sameera-Madhushan/Digger](https://github.com/Sameera-Madhushan/Digger)**
Cloning into 'Digger'...
remote: Enumerating objects: 29, done.
remote: Counting objects: 100% (29/29), done.
remote: Compressing objects: 100% (28/28), done.
remote: Total 29 (delta 14), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (29/29), done.

Вы должны получить каталог Digger после клонирования.

$ **tree Digger**
Digger
├── digger.py
├── LICENSE
├── README.md
└── requirements.txt
0 directories, 4 files

### Как использовать инструмент сбора информации Digger

cd Digger

### Установка зависимостей Python

sudo pip3 install -r requirements.txt

После установки зависимостей запустите скрипт Digger Python:

python3 digger.py

При запуске скрипта вы должны получить вывод, как показано ниже.

\_\_\_\_\_  \_
|  \_\_ \\(\_)
| |  | |\_  \_\_ \_  \_\_ \_  \_\_\_ \_ \_\_
| |  | | |/ \_\` |/ \_\` |/ \_ \\ '\_\_|
| |\_\_| | | (\_| | (\_| |  \_\_/ |
|\_\_\_\_\_/|\_|\\\_\_, |\\\_\_, |\\\_\_\_|\_|
           \_\_/ | \_\_/ |       v2.0
          |\_\_\_/ |\_\_\_/

\[Coded By Sameera a.k.a άλφα Χ\]

    {1} Whois lookup
    {2} Traceroute
    {3} DNS Lookup
    {4} Reverse DNS Lookup
    {5} GeoIP Lookup
    {6} Port Scan
    {7} HTTP Header Check
    {8} URL Extractor
    {9} robots.txt Checker
    {10} Update
    {11} Exit

Digger:-

Теперь используйте цифры, указанные для анонимного выполнения вашей сбор информации. См. Примеры ниже.

### Выполните поиск Whois

Digger:- 1
Do You Wish to Dig Your Own Information\[yes/no\] - no
Enter IP or Domain for lookup:- [google.com](http://google.com/)
	 \[#\] Checking the availability of API server...
	 \[#\] API Server is Online
   Domain Name: [GOOGLE.COM](http://google.com/)
   Registry Domain ID: 2138514\_DOMAIN\_COM-VRSN
   Registrar WHOIS Server: [whois.markmonitor.com](http://whois.markmonitor.com/)
   Registrar URL: [http://www.markmonitor.com](http://www.markmonitor.com/)
   Updated Date: 2018-02-21T18:36:40Z
   Creation Date: 1997-09-15T04:00:00Z
   Registry Expiry Date: 2020-09-14T04:00:00Z
   Registrar: MarkMonitor Inc.
   Registrar IANA ID: 292
   Registrar Abuse Contact Email: abusecomplaints@markmonitor.com
   Registrar Abuse Contact Phone: +1.2083895740
   Domain Status: clientDeleteProhibited [https://icann.org/epp#clientDeleteProhibited](https://icann.org/epp#clientDeleteProhibited)
   Domain Status: clientTransferProhibited [https://icann.org/epp#clientTransferProhibited](https://icann.org/epp#clientTransferProhibited)
   Domain Status: clientUpdateProhibited [https://icann.org/epp#clientUpdateProhibited](https://icann.org/epp#clientUpdateProhibited)
   Domain Status: serverDeleteProhibited [https://icann.org/epp#serverDeleteProhibited](https://icann.org/epp#serverDeleteProhibited)
   Domain Status: serverTransferProhibited [https://icann.org/epp#serverTransferProhibited](https://icann.org/epp#serverTransferProhibited)
   Domain Status: serverUpdateProhibited [https://icann.org/epp#serverUpdateProhibited](https://icann.org/epp#serverUpdateProhibited)
   Name Server: [NS1.GOOGLE.COM](http://ns1.google.com/)
   Name Server: [NS2.GOOGLE.COM](http://ns2.google.com/)
   Name Server: [NS3.GOOGLE.COM](http://ns3.google.com/)
   Name Server: [NS4.GOOGLE.COM](http://ns4.google.com/)
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: [https://www.icann.org/wicf/](https://www.icann.org/wicf/)
>>> Last update of whois database: 2018-12-05T05:09:08Z <<<

For more information on Whois status codes, please visit [https://icann.org/epp](https://icann.org/epp)

The Registry database contains ONLY .COM, .NET, .EDU domains and
Registrars.

### Выполнение поиска DNS

Digger:- 3
Enter Domain - [google.com](http://google.com/)
	 \[#\] Checking the availability of API server...
	 \[#\] API Server is Online
[google.com](http://google.com/).		299	IN	A	216.58.217.174
[google.com](http://google.com/).		299	IN	AAAA	2607:f8b0:4004:80e::200e
[google.com](http://google.com/).		299	IN	TXT	"docusign=05958488-4752-4ef2-95eb-aa7ba8a3bd0e"
[google.com](http://google.com/).		599	IN	MX	50 [alt4.aspmx.l.google.com](http://alt4.aspmx.l.google.com/).
[google.com](http://google.com/).		59	IN	SOA	[ns1.google.com](http://ns1.google.com/). [dns-admin.google.com](http://dns-admin.google.com/). 224084865 900 900 1800 60
[google.com](http://google.com/).		599	IN	MX	30 [alt2.aspmx.l.google.com](http://alt2.aspmx.l.google.com/).
[google.com](http://google.com/).		3599	IN	TXT	"facebook-domain-verification=22rm551cu4k0ab0bxsw536tlds4h95"
[google.com](http://google.com/).		21599	IN	CAA	0 issue "pki.goog"
[google.com](http://google.com/).		21599	IN	NS	[ns3.google.com](http://ns3.google.com/).
[google.com](http://google.com/).		21599	IN	NS	[ns2.google.com](http://ns2.google.com/).
[google.com](http://google.com/).		599	IN	MX	20 [alt1.aspmx.l.google.com](http://alt1.aspmx.l.google.com/).
[google.com](http://google.com/).		21599	IN	NS	[ns4.google.com](http://ns4.google.com/).
[google.com](http://google.com/).		599	IN	MX	10 [aspmx.l.google.com](http://aspmx.l.google.com/).
[google.com](http://google.com/).		3599	IN	TXT	"v=spf1 include:\_spf.google.com ~all"
[google.com](http://google.com/).		599	IN	MX	40 [alt3.aspmx.l.google.com](http://alt3.aspmx.l.google.com/).
[google.com](http://google.com/).		21599	IN	NS	[ns1.google.com](http://ns1.google.com/).

Следуйте той же схеме, чтобы выполнять сбор другой информации, поддерживаемые Digger.
