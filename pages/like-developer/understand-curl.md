---
title: Глубокое понимание curl
last_updated: Oct 8, 2019
sidebar: mydoc_sidebar
permalink: understand-curl.html
folder: like-developer
---

Почти каждый API показывает, как взаимодействовать с API с помощью curl. Поэтому, прежде чем двигаться дальше, давайте немного остановимся и узнаем больше о curl.

<a name="why"></a>
## Почему curl?

Одним из преимуществ API REST является то, что вы можете использовать практически любой язык программирования для вызова конечной точки. Конечная точка - это просто ресурс, расположенный на веб-сервере по определенному пути.

Каждый язык программирования имеет свой способ совершать веб-вызовы. Вместо того, чтобы тратить силы, пытаясь показать, как совершать веб-вызовы на Java, Python, C ++, JavaScript, Ruby и т.д., можно просто использовать curl.

curl предоставляет общий, независимый от языка способ демонстрации HTTP-запросов и ответов. Пользователи могут видеть формат запроса, включая любые заголовки и другие параметры. Можно перевести результат в определенный формат для используемого языка.

<a name="getPage"></a>
## Используем curl для получения веб-страницы

Как упоминалось ранее, одна из причин, по которой API REST настолько знакомы, заключается в том, что REST следует той же модели, что и веб (см. [Что такое REST API?](what-is-rest-api.html)). При вводе http-адреса в адресную строку браузера мы говорим браузеру сделать HTTP-запрос к ресурсу на сервере. Сервер возвращает ответ, а наш браузер преобразует ответ в более визуальное отображение. Но мы также можем увидеть и сырой код.

Чтобы увидеть пример того, как curl извлекает веб-ресурс, откройте терминал и введите следующее:

```
curl http://example.com
```

Curl извлечет HTML-код для сайта [example.com](http://example.com/). Задача браузера - сделать этот код визуально читаемым. curl показывает, что мы на самом деле получаем.

<a name="headers"></a>
## Запросы и ответы содержат заголовки

При вводе адреса веб-сайта мы видим только текст ответа. Но на самом деле происходит гораздо больше процессов. Когда мы делаем запрос, мы отправляем заголовок запроса, который содержит информацию о запросе. Ответ также содержит заголовок ответа.

Для того чтобы увидеть заголовок ответа в запрос curl, добавим ключ `-i`:

```
curl http://example.com -i
```

Заголовок будет включен над телом ответа:

```javascript
~/projects $ curl http://example.com -I
HTTP/1.1 200 OK
Content-Encoding: gzip
Accept-Ranges: bytes
Cache-Control: max-age=604800
Content-Type: text/html; charset=UTF-8
Date: Tue, 04 Dec 2018 04:35:43 GMT
Etag: "1541025663+gzip"
Expires: Tue, 11 Dec 2018 04:35:43 GMT
Last-Modified: Fri, 09 Aug 2013 23:54:35 GMT
Server: ECS (sjc/4F91)
X-Cache: HIT
Content-Length: 606
```

Чтобы в ответе получить только заголовок, используем ключ `-I`

```
curl http://example.com -I
```

Заголовок содержит метаданные ответа. Вся эта информация передается в браузер при запросе URL в нашем браузере (например, при просмотре веб-страницы в Интернете), но браузер не отображает эту информацию. Можно просмотреть информацию заголовка с помощью [консоли Chrome Developer Tools](https://developers.google.com/web/tools/chrome-devtools/console/), перейдя на вкладку `Network`.

Теперь давайте уточним метод. Метод GET (чтение) подразумевается по умолчанию, когда не указан другой метод, но мы сделаем это здесь явно с параметром -X:

```
curl -X GET http://example.com -I
```

При посещении веб-сайта мы отправляем запрос, используя метод GET. Существуют и другие методы HTTP, которые можно использовать при взаимодействии с REST API. Вот общие методы, используемые при работе с конечными точками REST:

| HTTP метод | Описание |
|:--|:--|
| POST | Создание ресурса |
| GET | Чтение (получение) ресурса |
| PUT | Обновление ресурса |
| DELETE | Удаление ресурса |

{% include note.html content="Метод GET используется по умолчанию в запросах curl. При использовании curl для выполнения запросов HTTP, отличных от GET, необходимо указывать нужный метод HTTP." %}

<a name="Unpacking"></a>
## Распаковка curl запроса API сервиса прогноза погоды

Рассмотрим подробнее запрос, который сделали в API сервиса прогноза погоды в предыдущем разделе [Создание curl запроса](make-curl-call.html):

```javascript
curl -X GET -H "Cache-Control: no-cache" -H "Postman-Token: 930d08d6-7b2a-6ea2-0725-27324755c684" "https://api.openweathermap.org/data/2.5/weather?zip=95050&appid=fd4698c940c6d1da602a70ac34f0b147&units=imperial"
```

У curl есть сокращенные имена для различных опций, которые вы включаете в свой запрос.

Вот расшифровка команд:

- `-Х GET`. `-X` Показывает, что в запросе будет использоваться какой-то метод. Общие параметры: `GET`, `POST`, `DELETE`, `PUT`. (Также можете увидеть использование `--get`. Большинство команд curl имеют несколько разных представлений. `-X GET` также можно записать как `--get`.);
- `-H` Отправляет пользовательский заголовок. Для каждого заголовка пары ключ-значение включаем дополнительный `-H`.

<a name="query"></a>
## Строки запроса и параметры

Параметры почтового индекса (`zip`), идентификатора приложения (`appid`) и единиц (`units`) были переданы в конечную точку с помощью «строк запроса». Знак `?` добавленный к URL указывает начало строки запроса. Параметры строки запроса - это параметры, которые появляются после знака `?`:

```
?zip=95050&appid=fd4698c940c6d1da602a70ac34f0b147&units=imperial
```

После строки запроса каждый параметр объединяется с другими параметрами через символ амперсанда `&`. Порядок параметров в строке запроса не имеет значения. Порядок имеет значение только в том случае, если параметры находятся *слева от строки запроса* (и, следовательно, являются частью самого URL-адреса). Любые настраиваемые части конечной точки, которые появляются перед строкой запроса, называются [параметрами пути](**INSERT LINK**) (разберем их позже).

<a name="curlCommands"></a>
## Общие команды curl, связанные с REST

У curl много возможных команд, но при работе с REST API наиболее распространены следующие:

| curl Команда | Описание | Пример |
|:--|:--|:--|
| `i` или `--include` | Включает заголовки ответа в ответ  | `curl -i http://www.example.com` |
| `-d` или `--data` | Включает данные для публикации на URL. Данные должны быть в [кодировке URL](https://www.w3schools.com/tags/ref_urlencode.asp). Данные также могут быть переданы в теле запроса.  | `curl -d "data-to-post" http://www.example.com ` |
| `-H` или `--header` | Отправляет заголовок запроса на ресурс. Заголовки являются общими с запросами REST API, потому что авторизация обычно включается в заголовок | `curl -H "key:12345" http://www.example.com` |
| `-X POST` | Задает метод HTTP для использования с запросом (в данном примере, `POST`). При использовании `-d` в запросе, curl автоматически указывает метод POST. С GET-запросами, в том числе HTTP-метод является необязательным, поскольку GET-метод используется по умолчанию | `curl -X POST -d "resource-to-update" http://www.example.com` |
|`@filename` | Загружает контент из файла | `curl -X POST -d @mypet.json http://www.example.com` |

В [документации curl](https://curl.haxx.se/docs/manpage.html) есть полный список команд curl.

<a name="comandExample"></a>
## Пример curl команды

Вот пример запроса curl, который объединяет некоторые из этих команд:

``` javascript
curl -i -H "Accept: application/json" -X POST -d "{status:MIA}" http://personsreport.com/status/person123
```

Запрос также может быть отформатирован с разрывом строки, чтобы быть более читабельным:

``` javascript
curl -i \
     -H "Accept: application/json" \
     -X POST \
     -d "{status:MIA}" \
     http://personsreport.com/status/person123 \
```
Разрывы строк является проблемой для Windows, поэтому лучше не форматировать запросы curl.


Заголовок `Accept` сообщает серверу, что в ответе мы принимаем только формат JSON.

<a name="quiz"></a>
## 👨‍💻 Опрос

Проверьте свою внимательность. Что означают следующие параметры?

- `-i`
- `-H`
- `-X POST`
- `-d`

{% include tip.html content="Использование curl в Терминале или [iTerm](https://www.iterm2.com/) OS Mac обеспечивают намного более простую работу, чем использование командной строки в Windows. Если серьезно относиться к документации API, работая на ПК, стоит подумать о переходе с OS Windows. Будет много утилит, которые мы установим при помощи Терминала, который *просто работает* на Mac. Кроме того, находясь в Силиконовой долине, и используя ПК вместо Mac можно показаться старомодным для окружающих (см. [Почему большинство стартапов покупают MacBook для своих сотрудников](https://www.quora.com/Why-do-most-startups-purchase-MacBooks-for-their-employees))." %}

Для более подробного изучения curl в документировании REST API можно посмотреть [REST-esting with curl](http://blogs.plexibus.com/2009/01/15/rest-esting-with-curl/).

[🔙](make-curl-call.html)

[Go next ➡](use-methods-with-curl.html)
