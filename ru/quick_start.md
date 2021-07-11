**[Оглавление](index) 	>	Знакомство**

## 1.1 Что такое PCAPdroid

PCAPdroid это инструмент с открытым исходным кодом, предназначенный для захвата и мониторинга собственного трафика без необходимости получения Root-прав на устройстве. Типичные сценарии использования:

- Анализ соединений, созданных приложениями установленными на устройстве (как пользовательскими, так и системными)
- Создание дампа сетевого трафика (PCAP-файл) приложения, которому вы не особо доверяете и последующий его анализ в стороннем приложении (например Wireshark на ПК)
- Расшифровка HTTPS/TLS трафика приложения с целью отладки или реверс-инженеринга

PCAPdroid использует [системный сервис VPN](https://developer.android.com/reference/android/net/VpnService) для получения всего трафика генерируемого Android-приложениями. Никаких серверов за пределами устройства не используется, благодаря механизму VPN приложение пропускает весь трафик через себя и благодаря этому позволяет получать данные для анализа.

**Важно**: PCAP-файл генерируемый приложением PCAPdroid не является на 100% корректным. Для получения сведений на этот счет посетите раздел [Надежность PCAP](quick_start#14-надежность-pcap).

## 1.2 Основы использования

<p align="center">
<img src="./images/main-screen.jpg" width="200" />
</p>


Для того чтобы начать пользоваться PCAPdroid, Вам необходимо нажать на кнопку запуска захвата (треугольник слева от кнопки настроек).

При первом запуске будет отображен диалог подтверждения VPN соединения. После его подтверждения PCAPdroid начнет захватывать трафик. Далее вы можете оставить PCAPdroid работать в фоне, пока будете работать с необходимыми приложениями - он продолжит работу в виде сервиса до тех пор, пока Вы не остановите захват трафика. Все время работы PCAPdroid в шторке уведомлений будет отображаться значок ключа (обозначает активное VPN соединение), значок может отличаться в зависимости от системы - чистый Android, кастомные прошивки, оболочки от производителей и т.п. Более того, у вас будет отображаться постоянное уведомление, которое будет содержать некоторые детали о захвате трафика (объем трафика и количество соединений).

По умолчанию захватываемый трафик будет обрабатываться HTTP-сервером, запускающемся на порту 8080. Далее Вы можете посетить указанный URL с другого устройства (например ПК) и начать загрузку PCAP-файла. Вы будете видеть прогресс загрузки как 0% потому, что браузер не знает какого размера целевой файл будет. Данные идут потоком, поэтому загрузка завершится сразу после того как Вы остановите захват трафика в PCAPdroid (т.е. поток прекратится). Так же следует помнить, что дамп полученный таким образом не содержит данных, сгенерированных приложениями до того, как браузер начал загрузку. 

**Важно:** HTTP-сервер отвечает на все запросы со стороны браузеров в локальной сети. Это означает что любой пользователь в вашей локальной сети попав на адрес HTTP-сервера (из PCAPdroid) может загрузить к себе копию вашего дампа! Если Вы хотите избежать этого, нажмите на "HTTP сервер" на главном экране приложения и выберите вариант "Никакой" или же "PCAP файл".

Весь захваченный трафик так же отображается во вкладке "Соединения".

<p align="center">
<img src="./images/connections.jpg" width="200" />
</p>


Каждая строка представляет исходящее соединение сделанное приложением или системой. Отображаются следующие сведения:

  - Значок приложения либо вопросительный знак если приложение не опознано
  - Название приложения
  - Протокол соединения, порт и версию протокола IP (если это не IPv4). Эти данные определяются анализом пакетов с помощью [nDPI](https://github.com/ntop/nDPI).
  - The SNI (имя хоста к которому отправлено соединение) или DNS запрос, если доступно. В остальных случаях - целевой IP.
  - Статус соединения - индикатор имеет значения "Открыто", "Закрыто", "Ошибка" или "Недоступно".
  - Время последнего полученного пакета связанного с соединением.
  - Общее количество трафика связанного с соединением.

Нажав на строку соединения можно получить больше деталей о нем.

<p align="center">
<img src="./images/connection-details.jpg" width="200" />
</p>
Некоторые детали, а именно IP-адрес, статус и статистика показываются всегда. Другая информация, например URL связанный с соединением отображается только когда это возможно. Так же PCAPdroid по возможности отображает данные переданные в открытом виде в начале соединения. Например в случае HTTP соединений будет так же отображен сам HTTP-запрос.

Во время захвата PCAPdroid держит всю информацию в памяти. По достижению предела, информация о старых соединениях будет удалена для освобождения памяти под новые соединения. Так же будет отображено сообщение о количестве удаленных по данной причине соединений. Общая информация о количестве трафика сгенерированного приложениями можно посмотреть в разделе "Приложения".

<p align="center">
<img src="./images/apps.jpg" width="200" />
</p>

Нажав на строку с конкретным приложением Вы сможете посмотреть все соединения сделанные только этим приложением.

## 1.3 Фильтры

Перед запуском захвата трафика, во вкладке "Состояние" можно выбрать целевое приложение (трафик которого необходим) посредством опции "Фильтр приложений". В таком случае лишь трафик выбранного приложения будет проходить через PCAPdroid. В основном это крайне полезно вместе с использованием [расшифровки TLS ](tls_decryption) чтобы убедиться, что будет расшифрован только нужный трафик.

После запуска захвата, PCAPdroid предоставляет несколько способов отфильтровать отображаемые соединения:

- через строку поиска можно отфильтровать соединения по IP-адресу, хосту, протоколу, названию приложения или значению UID. Удобный способ использования этого фильтра - найти нужное соединение, открыть его контекстное меню (удерживание нужной строки) и затем выбрать вариант "Поиск". Вам будут предложены варианты поиска указанным выше критериям кроме UID
- в окне "Приложения" (в боковом меню) можно увидеть все приложения трафик которых был захвачен к текущему моменту. Таким образом можно увидеть все соединения сделанные выбранным приложением.
- в контекстном меню приложения можно добавить одно из указанных выше полей в *белый список*.

Функция "Белый список" позволяет создавать фильтры по которым из общего списка соединений будут скрываться целые группы. Если правильно настроить белый список, то можно скрыть из списка ненужные соединения (прим. проверка обновлений, загрузка профиля и т. д.), оставляя тем самым только нужную часть трафика. Таким образом упрощается обнаружение целевых соединений (отсеяв ненужные) и поиск посторонней активности (скрыв все доверенные соединения).

Соединения попадающие под фильтры белого списка по прежнему захватываются PCAPdroid, просто не отображаются в списке. Во вкладке соединения наверху есть кнопка с иконкой глаза - нажатие на данную кнопку позволяет отобразить/скрыть соединения попадающие под фильтры белого списка. Независимо от того отображаются они или нет, данные соединения попадут в результирующий дамп (PCAP-файл) и будут видны при анализе сторонними инструментами.

Белый список существует постоянно (не только в контексте захвата), а так же может быть отредактирован в соответствующем окне, перейти к которому можно из бокового меню.

<p align="center">
<img src="./images/whitelist.jpg" width="200" />
</p>

## 1.4 Надежность PCAP

*Примечание: данная информация не касается [захвата трафика в Root режиме](advanced_features#44-захват-трафика-с-правами-root).*

PCAP генерируемый PCAPdroid содержит некоторые синтетические данные, которые могут быть ненадежными в случае детального анализа пакетов. В частности только полезная нагрузка (payload) пакета, одноранговые IP адреса и L3 порты являются актуальными данными. Это ограничение присуще способу используемому PCAPdroid, как и любому другому приложению ведущему захват без Root прав.

Некоторые из таких ограничений:

- Все пакеты приходящие из сети содержат синтетические IP и TCP/UDP заголовки
- Некоторые IP и TCP возможности могут быть отключены или иметь другую реализацию
- Размеры пакетов не соответствуют исходным