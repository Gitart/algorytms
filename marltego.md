# Знакомимся с Maltego

![](https://itsecforu.ru/wp-content/uploads/2021/10/maltego-870x400.jpg) [Аудит ИБ](https://itsecforu.ru/category/%d0%b0%d1%83%d0%b4%d0%b8%d1%82-%d0%b8%d0%b1/)

Автор Martyshkin На чтение 6 мин Опубликовано 29.10.2021

Содержание

1.  [Что такое Maltego ?](https://itsecforu.ru/2021/10/29/%d0%b7%d0%bd%d0%b0%d0%ba%d0%be%d0%bc%d0%b8%d0%bc%d1%81%d1%8f-%d1%81-maltego/#chto-takoe-maltego)
2.  [Как пользоваться Maltego](https://itsecforu.ru/2021/10/29/%d0%b7%d0%bd%d0%b0%d0%ba%d0%be%d0%bc%d0%b8%d0%bc%d1%81%d1%8f-%d1%81-maltego/#kak-polzovatsya-maltego)
3.  [Что делает Maltego?](https://itsecforu.ru/2021/10/29/%d0%b7%d0%bd%d0%b0%d0%ba%d0%be%d0%bc%d0%b8%d0%bc%d1%81%d1%8f-%d1%81-maltego/#chto-delaet-maltego)
4.  [Чем Maltego может быть полезным для нас?](https://itsecforu.ru/2021/10/29/%d0%b7%d0%bd%d0%b0%d0%ba%d0%be%d0%bc%d0%b8%d0%bc%d1%81%d1%8f-%d1%81-maltego/#chem-maltego-mozhet-byt-poleznym-dlya-nas)
5.  [Настройка Maltego в Kali Linux](https://itsecforu.ru/2021/10/29/%d0%b7%d0%bd%d0%b0%d0%ba%d0%be%d0%bc%d0%b8%d0%bc%d1%81%d1%8f-%d1%81-maltego/#nastroyka-maltego-v-kali-linux)
6.  [Запуск Maltego на Kali Linux](https://itsecforu.ru/2021/10/29/%d0%b7%d0%bd%d0%b0%d0%ba%d0%be%d0%bc%d0%b8%d0%bc%d1%81%d1%8f-%d1%81-maltego/#zapusk-maltego-na-kali-linux)
7.  [Заключение](https://itsecforu.ru/2021/10/29/%d0%b7%d0%bd%d0%b0%d0%ba%d0%be%d0%bc%d0%b8%d0%bc%d1%81%d1%8f-%d1%81-maltego/#zaklyuchenie)

### Что такое Maltego ?

Maltego – один из самых известных OSINT-фреймворков для персональной и корпоративной разведки. Это инструмент с графическим интерфейсом, который обеспечивает возможность сбора информации о любых лицах путем извлечения информации, общедоступной в Интернете, различными методами. Maltego также способен составлять списки DNS и собирать данные из социальных сетей в легко читаемом формате.

Как мы собираемся использовать [Maltego](https://itsecforu.ru/2018/04/28/%d0%b2%d1%8b%d0%bf%d0%be%d0%bb%d0%bd%d0%b5%d0%bd%d0%b8%d0%b5-%d1%81%d0%ba%d0%b0%d0%bd%d0%b8%d1%80%d0%be%d0%b2%d0%b0%d0%bd%d0%b8%d1%8f-%d1%83%d1%8f%d0%b7%d0%b2%d0%b8%d0%bc%d0%be%d1%81%d1%82%d0%b5%d0%b9/) в нашем тестировании на проникновение или в тренировке “red team”? Мы можем использовать этот инструмент для  визуализации собранных нами данных. Community-версия Maltego идет в коробке с Kali Linux.

### Как пользоваться Maltego

Задачи в Maltego называются transforms. Transforms встроены в инструмент и определяются как сценарии кода, которые выполняют определенные задачи. В Maltego также имеется множество плагинов, таких как набор инструментов SensePost, Shodan, VirusTotal, ThreatMiner и так далее. Maltego предлагает пользователю исчерпывающую информацию.

> “Информация – это рычаг. Информация – это сила. Информация – это Maltego.”

### Что делает Maltego?

Maltego – это программа, которая может быть использована для определения отношений и реальных связей между:

*   Людьми
*   Группами людей (социальные сети)
*   Компаниями
*   Организациями
*   Веб-сайтами
*   Инфраструктура Интернета, такая как:
*   Домены
*   DNS-имена
*   Поддержка сущностей NetBlocks
*   IP-адреса
*   Фразы
*   Аффилиации
*   Документы и файлы
*   Cущности связаные между собой с помощью открытых источников.

Maltego легко и быстро установить – он использует Java, поэтому работает на Windows, Mac и Linux. Maltego предоставляет вам графический интерфейс, который делает просмотр этих связей мгновенным и точным, позволяет также увидеть скрытые связи.
Используя графический интерфейс пользователя (GUI), вы можете легко увидеть взаимосвязи – даже если они находятся на расстоянии трех или четырех степеней разделения.
Maltego уникален тем, что использует мощную, гибкую структуру, которая формируется благодаря настройкам, поэтому, Maltego может быть адаптирован к вашим собственным, уникальным требованиям.

### Чем Maltego может быть полезным для нас?

Maltego можно использовать на этапе сбора информации во всех работах, связанных с безопасностью. Это сэкономит наше время и позволит вам работать более точно и умно.
Maltego помогает нам в вашем мыслительном процессе, наглядно демонстрируя взаимосвязи между искомыми элементами.
Maltego предоставляет нам гораздо более мощный поиск, обеспечивая более разумные результаты.
Если доступ к “скрытой” информации определяет ваш успех, Maltego поможет нам обнаружить ее.

### Настройка Maltego в Kali Linux

Самый простой способ получить доступ к этому приложению – ввести maltego в Терминале, также мы можем открыть его из меню приложений Kali Linux.

maltego

После того, как мы впервые открыли Maltego, он покажет нам страницу выбора продукта, где мы можем купить различные версии Maltego, но Community версия Maltego бесплатн и поэтому мы выбираем ее (Maltego CE) и нажимаем на кнопку запуска, как показано на следующем снимке экрана:

![](https://itsecforu.ru/wp-content/uploads/2021/10/1-1.jpg)

После нажатия на кнопку “RUN” мы получим окно настройки Maltego. Здесь нам нужно войти в систему и настроить Maltego в первый раз. Сначала нам нужно принять условия и положения Maltego, как показано на следующем снимке экрана:

![](https://itsecforu.ru/wp-content/uploads/2021/10/2-1.jpg)

На скриншоте выше видно, что мы установили флажок ✅ в поле “Accept” и нажали на кнопку “Next”.
Теперь необходимо зарегистрироваться для создания учетных данных (если ее нет). Нам нужно нажать на “Register”, и страница регистрации откроется в нашем браузере. Вводим учетные данные, подтверждаем регистрацию на почте и логинимся.

Затем нам нужно просто нажать “Далее”, “Далее”, “Далее”, “Далее”, и перед нами откроется Maltego, как мы видим на следующем снимке экрана.

![](https://itsecforu.ru/wp-content/uploads/2021/10/3-1.jpg)

### Запуск Maltego на Kali Linux

Теперь мы готовы использовать Maltego и запустить машину, перейдя к “Machines” в папке Menu и нажав на “Run Machine”; после чего мы сможем запустить экземпляр движка Maltego. Это показано на следующем снимке экрана:

![](https://itsecforu.ru/wp-content/uploads/2021/10/4-1.jpg)

Обычно, когда мы выбираем Maltego Public Servers, у нас есть следующие варианты выбора Machine:

*   Company Stalker: чтобы получить все адреса электронной почты в домене и затем посмотреть, какие из них резолвятся в социальных сетях. Он также загружает и извлекает метаданные опубликованных в Интернете документов.
*   Find Wikipedia edits: Это функция ищет псевдоним из Википедии и ищет его во всех платформах социальных сетей.
*   Footprint L1: определяет базовые отпечатки домена.
*   Footprint L2: определяет отпечатки среднего уровня домена .
*   Footprint L3: интенсивное глубокое погружение в домен, обычно используется с осторожностью, поскольку съедает все ресурсы.
*   Footprint XXL: Работает с крупными целями, такими как компания, имеющая собственные центры обработки данных, и пытается получить отпечатоки, просматривая записи sender policy framework (SPF) в надежде на netblocks, а также обратные делегированные DNS к их серверам имен.
*   Person – Email Address:: Получить чей-то адрес электронной почты и посмотреть, где он используется в Интернете. Вводится не домен, а полный адрес электронной почты.
    URL – Сеть и доменная информация: Это преобразование позволяет определить доменную информацию других ДВУ. Например, если мы предоставим www.yandex.ru, оно определит www.yandex.com, yandex.kz, и так далее и тому подобное.

Эксперты по кибербезопасности обычно начинают с “Footprint L1”, чтобы получить базовое представление о домене и его потенциально доступных субдоменах и соответствующих IP-адресах. В рамках сбора информации неплохо начать с этой информации, однако пентестеры могут использовать и все остальные Machine, как упоминалось ранее, для достижения своей цели. После выбора Machine нужно нажать кнопку “Next” и указать домен, например yandex.ru. На следующем скриншоте представлен обзор yandex.ru

![](https://itsecforu.ru/wp-content/uploads/2021/10/6-2.jpg)

В верхней левой части скриншота выше мы увидим окно Palette . В окне Palette мы можем выбрать тип сущности, для которой вы хотите собрать информацию. Maltego делит сущности на шесть групп следующим образом:

*   ***Devices*** Устройства, такие как телефон или камера.
*   ***Infrastructure***  Инфраструктура, такая как AS, DNS-имя, домен, IPv4-адрес, MX-запись, NS-запись, netblock, URL и веб-сайт.
*   ***Locations*** Местоположение на Земле.
*   ***Penetration ***Тестирование на проникновение
*   ***Personal*** Личные данные, такие как псевдоним, документ, адрес электронной почты, изображение, человек, номер телефона и фраза.
*   ***Social Network***  Социальные сети, такие как объект Facebook, объект Twitter, принадлежность к Facebook и принадлежность к Twitter.

Если мы щелкнем правой кнопкой мыши на имени домена, мы увидим все возможные варианты преобразований, которые можно сделать с именем домена:

![](https://itsecforu.ru/wp-content/uploads/2021/10/7-1.jpg)

*   DNS домена.
*   Данные владельца домена.
*   Адреса электронной почты домена.
*   Файлы и документы из домена.
*   Другие преобразования, такие как To Person, To Phone numbers и To Website.
*   Все даныее (All transforms)

Если мы хотим поменять домен, необходимо сначала сохранить текущий график. Чтобы сохранить график, нажмите на значок Maltego, а затем выберите Сохранить. График будет сохранен в формате файла графиков Maltego ( .mtgl ). Опциаонально доступно также шифрование файла AES-128

![](https://itsecforu.ru/wp-content/uploads/2021/10/8.jpg)

### Заключение

Вот как Maltego работает в нашей системе Kali Linux. Это очень мощный инструмент сбора информации с графическим интерфейсом, который поставляется вместе с Kali Linux.
