---
title: О сайте
description: Рассказываю о dxfoto.ru и как мы докатились до жизни такой
date: 2017-08-15
tldr: Старый пост с medium на двухлетие проекта
epigraph: Два года dxfoto.ru. Текст написан в 2017 году и немного порастерял актуальность.
tags: [dxfoto]
---

Нашему сайту, [www.dxfoto.ru](https://www.dxfoto.ru), нынче стукнуло два года. Надо признать, что это мой самый живучий pet-project, остальные заканчивались намно-ого быстрее.

Итак, два года кряду мы фоткаем, копаемся в архиве, обрабатываем, исследуем и ежедневно вывешиваем по фоточке. Правда, по поводу "ежедневно" --- не совсем справедливо. Иногда нам лень, иногда нас мучает творческий кризис, иногда отсутствует техническая возможность или что-то сломалось. Но потом мы навёрстываем упущенное (кстати, по-честному: фоточек из будущего у нас нет) и всё равно получается по фотке на день. То есть, сегодня у нас фото номер 732 (потому что прошлый год был високосным). А вот эта была первой:

<figure>
  <img src="https://img.dxfoto.ru/l/2015/08/2015-08-14.webp" alt="Брандмауэр (#1)" />
  <figcaption>Брандмауэр (#1)</figcaption>
</figure>

Сейчас я хочу рассказать, что у нас и как устроено. Просто потому что дата, и вообще. И да, shameless self-promotion.

## Про нас

Мы --- это я и моя драгоценная фрау, скрывающаяся под инициалами К.С. (она не очень любит светить лицом в интернетах, хотя найти её совсем не трудно). Мы не считаем себя профессиональными фотографами в том смысле, что не зарабатываем этим себе на хлеб. Так, увлечённые любители.

Нашей первой камерой была Olympus E-300, приобретённая импульсивно и за совершенно безумные по нашему тогдашнему (11, кажется, лет назад) бюджету деньги. Аппаратик за годы эксплуатации порядком ушатался, хотя в целом ещё живой. В помощь ему мы недавно подкупили чуть менее убитый Olympus E-3. К этому добру у нас есть некотрое количество объективов --- китовый 14–85mm, бестолковый телек 40–150mm и 35mm макрообъектив. Последний --- самый любимый, и с ним, пожалуй, сделана большая часть фотографий, как на сайте, так и вообще. Чтобы жилось веселее, у нас есть ещё Canon 70D. К нему телек 55–250mm на EF-S и блинчик 40mm на EF. И ещё есть фишай Зенитар МС 16mm, его можно куда угодно накрутить. А ещё мы мечтаем о Canon MP-E 65mm, микроскопе, телескопе и разной другой полезной фигне. Когда-нибудь. Потом.

С Олимпусом обычно ходит К.С., а с Кэноном — я, но это не обязательно.

<figure>
  <img src="https://img.dxfoto.ru/l/2017/01/2017-01-18.webp" alt="Горы (#524)" />
  <figcaption>Горы (#524)</figcaption>
</figure>

## Про процесс

Сначала фотки фоткаются (что очевидно). Потом я их старательно каталогизирую, для этого у меня есть DigiKam. Как вы, возможно, заметили, для живности и прочих цветочков мы любим определять видовую принадлежность. В этом нам непосильно помогает Википедия, iNaturalist, Catalogue of Life и поиск по картинкам в Гугле и Яндексе.

Перед публикацией картинка слегка марафетится в Darktable (тут уважаемая публика начинает подозревать, какой ОС я пользуюсь), он же используется для проявки равок.

Потом к картинке сочиняется какой-нибудь глупый и не сильно осмысленный текст (не уверен, что это нужно, но как-то так получилось). Из картинки выдёргиваются кое-какие нужные метаданные, и всё это добро собирается в публикацию. Дальше немножко программной магии, о ней ниже.

<figure>
  <img src="https://img.dxfoto.ru/l/2015/09/2015-09-20.webp" alt="Кузнечик (#38)" />
  <figcaption>Кузнечик (#38)</figcaption>
</figure>

## Про технологию

В основе сайта знакомый многим Jekyll. На самом деле, за время эксплуатации мне он перестал казаться таким расчудесным, как был прежде. Самая большая его проблема --- это язык шаблонов Liquid. Не то, чтобы он был плох сам по себе (ну, местами), но мне его регулярно не хватает. Иногда приходится городить страшные конструкции, чтобы сделать какие-то элементарные вещи. На скорость сборки это влияет, конечно, отрицательно. Прямо сейчас сайт пересобирается за 3 с лишним минуты. Стыдоба. Однажды я его перепишу на Nanoc, там хотя бы HAML есть. Ну, или ещё на чём-нибудь. Когда-нибудь.

Поскольку у нас Jekyll, первоначально сайт хостился на Github Pages. Но, всё из-за того же ограниченного функционала Liquid, пришлось дописывать свои плагинчики. GH этого не одобряет, пришлось искать другое решение. Пробовал размещаться на BitBucket с Aerobatic, но что-то у меня и с ними не срослось. Так я пришёл к нынешнему варианту. Исходники по-прежнему лежат в GH, оттуда после push их забирает Travis CI, на нём всё собирается и улетает в Amazon S3.

С хостингом фотографий тоже целая история. Изначально рассматривался Flickr --- они дают хороший объём, масштабируют и вообще. Но, к пущему моему недовольству, у загруженных фоток получается совершенно непредсказуемый URL, что не очень удобно. К тому же, по моим наблюдениям, фермы у них временами отваливаются, что мне совсем не по душе. Первым принятым вариантом был Imgur. Там, конечно, тоже непредсказуемый URL, но его проще достать. От них я отказался, когда предлагаемые сервисом размеры картинок перестали подходить под шаблон страниц. Так я пришёл в Cloudinary. У них прикольно --- ресайз, оптимизация, все дела. Но халявный тариф мы довольно быстро выбрали, а минимальный платный на тот момент был неприлично дорогим для некоммерческого сайта. Сейчас фоточки лежат всё в том же S3 (ну, кроме тех, что были в Cloudinary). Сначала в него заливается оригинал, это событие дёргает функцию в Lambda, которая создаёт задачу в Blitline. Blitline любезно ресайзит и слегка оптимизирует картинки и складывает их обратно в S3, плюс закидывает туда же извлечённый EXIF, потому что он нужен. Ещё снимки отправляются в Imagga, которая определяет, что на картинке, и выдёргивает основные цвета. Правда, прямо сейчас это не используется, но данные есть.

Впереди всего этого великолепия стоит Cloudflare, на тот случай, если Amazon вдруг помрёт (было один раз, все помнят). В общем-то, кроме AWS всё бесплатно. Amazon выставляет мне около 6 центов в месяц. По-моему, неплохо.

<figure>
  <img src="https://img.dxfoto.ru/l/2016/05/2016-05-29.webp" alt="Барбарис (#290)" />
  <figcaption>Барбарис (#290)</figcaption>
</figure>

Мне вообще лень заниматься раскруткой сайта, поэтому посещаемость у него --- 3.5 человека в неделю. Некоторых знаю лично. Есть ещё представительства в ВК (аж 8 подписчиков, включая авторов), FB (ровно 0, но фотки видны моим немногочисленным друзьяшкам), Twitter (там, кажется, четверо, но просмотров довольно много) и по старой памяти Imgur (самая активная публика, даже каменты пишут). С некоторых пор ещё осваиваю яндексов Дзен, там красивые циферки, но ссылок на сайт я не даю.

А ещё мы не размещаем рекламу, не следим за посетителями (это не мы, это Метрика и GA) и, если уж кому понадобятся наши фотки, не просим за них ничего кроме указания источника.

<figure>
  <img src="https://img.dxfoto.ru/l/2015/09/2015-09-25.webp" alt="Ежовик (#43)" />
  <figcaption>Ежовик (#43)</figcaption>
</figure>

Такие дела. На том и закончу эту писанину. Всем спасибо.