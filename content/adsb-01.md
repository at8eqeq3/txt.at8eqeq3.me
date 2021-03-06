---
title: ADS-B, часть 1
section: О самолётах и данных
description: Первая часть бесконечной истории о визуализации данных с ADS-B приёмника.
date: 2019-05-05
tldr: Я делал карту из табличных данных, и мне не очень понравилось.
epigraph: В этой части я рассказываю о задаче и первых попытках её решить.
links:
  - href: /adsb-02.html
    title: О самолётах и данных, часть 2
  - href: /adsb-03.html
    title: О самолётах и данных, часть 3
tags: [авиация, визуализация данных, gis]
cover: adsb-01-01.webp
---

## Введение

Однажды случилось так, что я влюбился в гражданскую авиацию. То есть, до какого-то момента в жизни авиация существовала без меня: самолёты летали по небу, я ходил пешком, и мы нигде не пересекались. Ну, было два раза: летал в отпуск и обратно. Путь туда не помню вовсе (не напивался, как часто бывает, а просто был трое суток не спавши, а там реальность уже не очень-то воспринимается), путь обратно честно проспал, потому что спёкся в автобусе, пока ехал в аэропорт. А потом я сменил работу, и как-то так оказалось, что мы достаточно активно общаемся со строившимся ещё на тот момент новым центром АУВД, что во Внуково, да и с их коллегами в других регионах тоже иногда. И что-то нахлобучило: а какие у нас самолётики летают, а как и куда они летят, а что там за [FlightRadar24](https://www.flightradar24.com), а как там вообще это всё организовано, и тому подобные вопросы. А в какой-то момент ещё и пришлось сменить место жительства, и теперь у меня прямо над головой, только что не цепляя за крышу, иногда пролетают борта, следующие в Шереметьево.

Всё это привело к тому, что мне захотелось водить самолёт. Объективно, мне это не светит --- по зрению меня не берут даже в машинисты электрички. Но есть симуляторы, что печально, но лучше, чем ничего вообще. У меня, как пользователя Linux, было аж целых два варианта: [FlightGear](http://home.flightgear.org/) бесплатно и [X-Plane](https://www.x-plane.com/) (на тот момент 10 версии) в Steam за лютые бабки. При всей моей прижимистости, каким-то непостижимым образом склонился ко второму варианту. Более того, с релизом 11 версии, купил и её. Совсем сдурел. Но она мне пригодилась и для другой вещи, о которой статья. Хочу отметить, что формат данных в 11 версии отличается от такового в предыдущих, но не сильно, так что всё описанное с минимальными модификациями подойдёт для всего.

Помимо вождения самолёта, мне хотелось следить за тем, что летает буквально над моей головой. Есть, конечно, fr24, но вы же меня понимаете, иначе зачем вы читаете эту писанину. Надо чтобы своё. Дендрофекально, кишочками наружу, но своё. Всё уже давно придумано, и для этой цели существует [RTL-SDR](https://www.rtl-sdr.com/), позволяющий относительно просто использовать приёмник спутникового ТВ с алиэкспресса за 500 рублей для почти чего угодно. Но есть нюансы --- в с виду одинаковых приёмниках могут стоять разные тюнеры, у которых различаются доступные частоты. Короче, первый заказанный приёмник не вывозил нужные 1090 МГц, и я просто скакал по частотам и искал что-нибудь интересное. Так я нашёл ACARS, о котором как-нибудь отдельно расскажу, потому что штука тоже интересная. Между тем, подъехал следующий приёмник, теперь уже правильный, была сооружена коллинеарная антенна, установлен софт --- сначала на NAS, а потом на RPi, потому что декодирование потока, как выяснилось, весьма прожорливо и мешает смотреть кинцо. В центре всего софтверного бардака работает [dump1090](https://github.com/flightaware/dump1090), обвешанный вокруг фидерами от fr24, [fa](https://flightaware.com/) и [rb24](https://www.radarbox24.com/). Собственно, dump1090 (точнее, его веб-морда) и будет, с позволения сказать, дорабатываться. Не он сам, но карта для него.

В какой-то момент я решил, что dump1090 не хватает аэронавигационной схемоты. Там можно что-то включить, но оно показывает только Штаты, а до туда у меня чё-т антеннка не добивает (однажды я вытащу её на крышу, обзор там великолепный, но до Штатов всё равно не достать). Хотелось бы поближе. Схемота есть у fr24 и rb24, но там только транзитные маршруты, а у меня тут три аэропорта вокруг, и схемы входа и выхода мне гора-аздо интереснее. Придётся городить самому, ну да не в первый раз, чё уж. Итак, имеем карту, сделанную на OpenLayers, с основным слоем из OSM, что, в принципе, сейчас не очень важно. OpenLayers умеет кучу разных форматов картографических данных, поэтому у нас есть выбор.

Вторая задача --- это источник данных. X-Plane выше я упомянул не просто так, данные я уволок именно оттуда. Они там немного тухлые, но никто, я подозреваю, от этого не помрёт. Можно, конечно, и свежими разжиться, но это стоит денег, что неприемлемо. Laminar Research, вероятно, не хотели бы, чтобы я их данными с кем-то делился, ну дык я этого и не буду делать. Моя копия показывается только мне, а уважаемой публике предлагается разжиться собственной копией.

Важно также отметить, что я работал с данными, используя язык Ruby, поскольку он мне нравится. Полный код, если доживёт до конца написания статейки, обретётся где-то на GitHub. Надеюсь, моих комментариев к нему, а также достаточно высокой читабельности кода на Ruby вообще, будет достаточно, чтобы читатель при желании мог воспроизвести те же действия на более близком ему языке.

## Исходные данные

Вокруг этих самых данных основной сыр-бор. Попытаюсь рассказать, как на их основе можно получить что-то визуально приемлемое. Подозреваю, что это не единственно верный, а возможно и не верный вовсе способ, но вот он есть. Итак, немного изучив вопрос, имеем в каталоге с симулятором набор файлов, которые нам интересны:

* `earth_nav.dat` --- радионавигационные средства (натурально радиостанции где-то стоят и что-то вещают, а самолёты на них ориентируются)
* `earth_fix.dat` --- точки пути (виртуальные точки в пространстве с заранее определёнными координатами, которые формируют разного рода маршруты. Могут иногда совпадать с реальными объектами из предыдущего пункта, наземными ориентирами, но чаще вообще ни с чем.
* `earth_awy.dat` --- воздушные трассы, соединяющие сущности из предыдущих двух пунктов. Да, самолёты не летают абы как, у них тоже есть шоссе (кроме пары стран в Европе, где от таких шоссе принципиально отказались, но как там у них в связи с этим УВД работает, я пока не в курсе).
* `cifp/*.dat` --- схемы входа и выхода для аэропортов, наиболее ценная для меня информация.

Для всех этих наборов есть [документация](https://developer.x-plane.com/docs/data-development-documentation/), но я всё равно кратко расскажу о наиболее важных моментах. Забавный факт: слово earth в названии файлов обозначает нашу родную планету, потому как теоретически симулятор может изобразить и другие, лишь бы кто-то там радиостанций понавтыкал.

Итак, все файлы суть текстовые, таблицы с разделителями. У первых трёх разделитель --- пробел, у остальных --- запятая. _"Е" --- Единообразие_. Пройдёмся по порядку.

### `earth_nav.dat`

Достаточно противный файл. В первую очередь, потому что описывает в одной куче достаточно разные объекты, имеющие разное количество полей, да и смысл этих полей не всегда одинаковый (но в 11 версии его заметно причесали, и теперь хотя бы самые важные поля вроде ИД и координат всегда на одних и тех же местах). Тут я использовал `String#split`, передавая ему вторым параметром количество полей в записи в зависимости от первых двух символов. Больше всего меня интересовали поля 1 и 2 (нумерация с 0), содержащие координаты, а также поле 7 --- идентификатор объекта. К сожалению, для однозначной идентификации объекта нужно ещё поле 9 (код региона из документа ICAO 7910) и поле 8 (ИД региона --- либо код аэропорта, либо строка `ENRT`). ИД объекта вроде как должен быть уникален в пределах регионального кода, но в аэропортах это часто не так: назвать маяки с двух торцов полосы A и B --- это как здрасте. Поэтому при разборе приходится идти на разнообразные хитрости, собирая идентификатор объекта из трёх полей (что нам ещё аукнется).

Поскольку городить карту на всю планету мне не хочется, я отбираю только записи с кодом региона UU (он как раз вокруг меня примерно поровну во все стороны, и ещё на север хвостик).

### `earth_fix.dat`

Тут попроще. 0 и 1 поле --- это координаты, 2 --- ИД, 3 --- ИД региона, 4 --- код региона, всё как у предыдущего. Можно, опять же, отфильтровать по ИД региона. На самом деле, это не совсем правильно, но никто, опять же, от этого не помрёт. Нам картинки смотреть, а не самолёты водить.

Разобранные данные из этих двух файлов понадобятся нам для двух вещей: первое --- точечные объекты на карте; второе --- сборка отрезков по ещё не разобранным файлам. В связи с первым пунктом я решил на ходу формировать подходящую структуру для вывода GIS-данных, формат которых в ходе долгих мучений был принят как geoJSON, просто потому что `IO.puts(something.toJSON)` и думать не надо. Хочется обратить внимание, что в geoJSON (да и во многих других форматах) координаты представлены в виде `[lon, lat]`, тогда как в исходном файле (и у меня в голове) широта указана прежде долготы, что я, конечно, сначала упустил и получил довольно странный результат. Ещё один противный факт про geoJSON: без properties у feature файлик невалидный, надо их всегда указывать, пусть даже там будет пустой ассоциативный массив. Ну, в какой-то момент я пришёл к выводу, что пропертя будут не пустые. Для точек я указываю, что они такое есть --- fix, радиомаяки или ещё что.

### `earth_awy.dat`

Относительно просто. Два набора полей (с 0 по 5), позволяющих, _помолясь_, идентифицировать точки в пространстве (ИД, код региона и тип), обозначающие начало и конец отрезка, и 10 поле с его названием. Тут мы тоже сразу формируем структуру для вывода отрезков, не забывая указывать в properties тип, пригодится.

### `cifp/*.dat`

Самые противные, но и самые интересные файлы. Противные, в большей мере, потому что формат в документации состоит из ссылок на различные участки документа ARINC SPECIFICATION 424. Это огромная портянка (её ещё поди-ка нагугли), описывающая некий формат данных, которые с нашими соотносятся только содержанием. Для общего развития можно ознакомиться полностью, но нас интересует довольно мало.

Вообще в этих файлах есть два принципиально различных типа записей --- RWY и всё остальное. RWY --- это параметры ВПП, а остальное --- процедуры. Формат их различается совсем. Сначала надо разобрать ВПП и добавить их ко всем имеющимся точечным объектам и, разумеется, пометить принадлежностью к аэропорту, иначе перепутаются. Неожиданно для всех, координаты точек в этих записях представлены не в виде градусов с десятичной дробью, а как градусы, минуты и секунды с двумя знаками после запятой, и всё это без каких-либо разделителей. Узнал я об этом, только собрав карту. Кот бы мог подумать.

Дальше можно разбирать процедуры. Нам нужно очень мало из предоставленных данных (они и так жиденькие). Процедуры представлены в виде списка секций, имеющих возрастающую нумерацию. Сначала мы разбираем, где какая процедура (SID, STAR или APPCH), выбираем её ИД и ИД маршрута, находим в списке точечных объектов, куда попадает очередная секция (тут нам помогают коды региона и знание, к какому вообще аэропорту относится процедурка) и лепим их в список отрезков. Некоторые секции волшебные --- предполагают следование по как-то хитро вычисленной дуге (тут мне было лень думать, и дуга вдруг стала прямой) или _векторение_ диспетчером (в его отсутствие тоже как-то прямовато получилось), и когда-нибудь мы это примем, но не сейчас.

Выше я говорил о проблеме с идентификацией точек, вот тут она и всплывает. В файле есть код региона. ИД региона _по идее_ должно подставляться из имени файла (оно совпадает с ICAO-кодом аэропорта, к которому относятся процедурки), но есть нюанс. Некоторые точки вполне себе ENRT, поэтому всегда надо проверять. Сначала ищем точку от аэропорта, потом ENRT, а если не нашли --- удивляемся и орём "Achtung!". Ещё временами попадаются точки из соседних регионов. Это нормально, но у нас их нет. Придётся просто забить на это.

Ну, короче. Если всё изначально собиралось как надо, в конце мы имеем большой и толстый geoJSON с точками и линиями. Всем хорош, но некрасив, а с учётом размера --- неприлично тормозит в браузере. Надо делать картинками.

## QGIS

Интересная такая софтинка для работы с GIS-данными всех сортов и расцветок. В частности, понимает и geoJSON как векторные слои. Что интересно, принципиально не хочет смешивать при импорте точки и линии, поэтому пришлось подгружать один файл дважды. Зато видит properties и позволяет по ним делать различное оформление для элементов --- цвет, символы и всё такое.

Собственно, тут мы накидываем слой OSM, чтобы видеть, где мы находимся, и два слоя с нашим geoJSON --- для точек и для линий. Настраиваем для них представления, фильтруя по свойству, которое мы добавили при разборе исходников. Для линий я также добавил различные, в зависимости от типа, смещения, чтобы они не сливались: погоды не делает, а наглядность повышается. Штатный функционал QGIS позволяет сгладить углы у линий. Это тоже делает карту более читаемой: сразу видно, где поворот, а где перекрёсток. Функция создаёт новый слой, так что копируем свойства представления с исходного слоя и отключаем его.

<figure>
  <img src="/img/adsb-01-01.webp" alt="Пример генерируемой карты" />
  <figcaption>Пример генерируемой карты</figcaption>
</figure>

Схема, да ещё и наложенная на карту --- штука красивая, но пока совершенно непрактичная. Самолёты по ней не летают, и я не уверен, что можно убедить QGIS показывать их в реальном времени. Ну, да не очень-то и хотелось, на самом деле. Нам нужно нарезать тайлы для OpenLayers. Штатно QGIS этого не может, но есть [плагин для этого](http://plugins.qgis.org/plugins/qtiles3/). Немного экспериментов с настройками, и вот мы уже активно раскладываем по каталогам квадратные картинки. Из минусов --- работает непристойно медленно. Устал ждать, пока нарежет тайлы для zoom level 8 и 9, на большее терпения уже не хватило.

Плагин раскладывает картинки сразу в структуру, совместимую с OpenLayers, поэтому надо их просто положить на любой http-сервер (например, на тот же lighttpd, которым предполагается по умолчанию отдавать морду dump1090) и натравить карту на соответствующий URL. Готово!

## Заключение

Цель, конечно, достигнута. Расстраивают следующие моменты:

* отсутствие некоторых точек, потому что они в другом регионе. В принципе, разбор происходит достаточно быстро, так что можно разобрать всё целиком, но, допустим, `cifp/*.dat` брать только нужные и отрисовывать только нужный участок карты;
* возможно, стоит генерировать отдельные слои для enroute и аэропортов, чтобы было меньше каши. Да и процедуры неплохо бы разделить;
* недостаточно автоматизации процесса. А это как раз тема для второй части.

*[ACARS]: Aircraft Communications Addressing and Reporting System
*[OSM]: OpenStreetMap
*[ICAO]: International Civil Aviation Organization
*[RWY]: runway
*[SID]: standard instrument departure
*[STAR]: standard instrument arrival
*[APPCH]: approach
*[GIS]: Geographic information system
*[NAS]: Network attached storage
*[УВД]: Управление воздушным движением
*[АУВД]: Автоматизированное управление воздушным движением