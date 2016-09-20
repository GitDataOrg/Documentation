# Справка по ZeroFrame API



# Обертка

_Эти команды обрабатываются оберточной конструкцией и не отправляются на сервер с помощью WebSocket_


#### wrapperConfirm _message, [button_caption]_
Отображает уведомление с кнопой подтверждения

Параметр               | Описание
                  ---  | ---
**message**            | Текст выводимого сообщения
**button_caption** (опционально) | Надпись кнопки подтверждения (по умолчанию: OK)

**Результат**: True в случае нажатия на кнопку

**Пример:**
```coffeescript
# Удалить сайт
siteDelete: (address) ->
	site = @sites[address]
	title = site.content.title
	if title.length > 40
		title = title.substring(0, 15)+"..."+title.substring(title.length-10)
	@cmd "wrapperConfirm", ["Вы уверены? <b>#{title}</b>", "Delete"], (confirmed) =>
		@log "Deleting #{site.address}...", confirmed
		if confirmed
			$(".site-#{site.address}").addClass("deleted")
			@cmd "siteDelete", {"address": address}
```


---


#### wrapperGetLocalStorage
**Результат**: Локальное хранилище браузера для сайта

**Пример:**
```coffeescript
@cmd "wrapperGetLocalStorage", [], (res) =>
	res ?= {}
	@log "Значение в локальном хранилище:", res
```


---

#### wrapperPermissionAdd _permission_

Запрашивает права доступа для сайта

Параметр         | Описание
             --- | ---
**permission**   | Наименование набора прав доступа (на пример. Merger:ZeroMe)

---


#### wrapperSetLocalStorage _data_
Устанавливает локальное хранилище для сайта

Параметр              | Описание
                 ---  | ---
**data**              | Любая структура данных, которую вам необходимо держать в хранилище

**Результат**: не возвращается

**Пример:**
```coffeescript
Page.local_storage["topic.#{@topic_id}_#{@topic_user_id}.visited"] = Time.timestamp()
Page.cmd "wrapperSetLocalStorage", Page.local_storage
```


---


#### wrapperNotification _type, message, [timeout]_
Выводит уведомление

Параметр               | Описание
                  ---  | ---
**type**               | Доступные типы сообщений: info, error, done
**message**            | Текст выводимого сообщения
**timeout** (опционально) | Скрыть сообщение по окончанию интервала (в милисекундах)

**Результат**: не возвращается

**Пример:**
```coffeescript
@cmd "wrapperNotification", ["done", "Ваша регистрационные данные были отправлены!", 10000]
```


---


#### wrapperPrompt _message, [type]_

Запрашивает у пользователя ввод текста

Параметр            | Описание
               ---  | ---
**message**         | Текст выводимого сообщения
**type** (optional) | Тип вводимого значения (по умолчанию: text)

**Результат**: Введенное значение

**Пример:**
```coffeescript
# Запрос секретного ключа
@cmd "wrapperPrompt", ["Пожалуйста, введите ваш секретный ключ:", "password"], (privatekey) =>
	$(".publishbar .button").addClass("loading")
	# Отправка подписанного content.json и отправка серверу запроса на публикацию
	@cmd "sitePublish", [privatekey], (res) =>
		$(".publishbar .button").removeClass("loading")
		@log "Реузльтат публикации:", res
```


#### wrapperSetViewport _viewport_

Установка мета-тега `viewport` (необходимо для мобильных устройств)


Параметр            | Описание
               ---  | ---
**viewport**        | Значение тега `viewport`

**Return**: None

**Пример:**
```coffeescript
# Установка мета-тега viewport
@cmd "wrapperSetViewport", "width=device-width, initial-scale=1.0"
```

---


# UiServer

UiServer для ZeroNet как серверный набор LAMP для обычных сайтов.

UiServer делает всю работу 'бэкенда' (на пример: запросы к базе данных, доступ к файловым ресурсам и так далее). Он содержить вызов API, необходимые для создания динамического контента сайтов.



#### certAdd _domain, auth_type, auth_user_name, cert_
Создает новые сертификат для текущего пользователя.

Параметр             | Описание
                 --- | ---
**domain**           | Домен, для которого выпускается сертификат
**auth_type**        | Способ авторизации при регистрации
**auth_user_name**   | Логин пользователя при регистрации
**cert**             | Сам сертификат: `auth_address#auth_type/auth_user_name` строка, подписанная сертификатом владельца сайта

**Результат**: "ok", "Not changed" или ошибку {"error": error_message}

**Пример:**
```coffeescript
@cmd "certAdd", ["zeroid.bit", auth_type, user_name, cert_sign], (res) =>
	$(".ui").removeClass("flipped")
	if res.error
		@cmd "wrapperNotification", ["error", "#{res.error}"]
```


---


#### certSelect _accepted_domains_
Отображение выбора сертификата.

Параметр             | Описание
                 --- | ---
**accepted_domains** | Список доменов, принимающих в качестве поставщиков авторизации

**Результат**: не возвращается

**Пример:**
```coffeescript
@cmd "certSelect", {"accepted_domains": ["zeroid.bit"]}
```


---


#### channelJoin _channel_

Запрос уведомлений о событиях сайта.

Параметр    | Описание
        --- | ---
**channel** | Используемый канал

**Результат**: None

**Каналы**:

 - **siteChanged** (используется по умолчанию)<br>События: peers_added, file_started, file_done, file_failed

**Пример**:
```coffeescript
# Подключение через WebSocket готово
onOpenWebsocket: (e) =>
	@cmd "channelJoinAllsite", {"channel": "siteChanged"}

# Маршрутизация входящих запросов и сообщений
route: (cmd, data) ->
	if cmd == "setSiteInfo"
		@log "Сайт изменен", data
	else
		@log "неизвестная команда", cmd, data
```

**Пример данных события**
```json
{
	"tasks":0,
	"size_limit":10,
	"address":"1RivERqttrjFqwp9YH1FviduBosQPtdBN",
	"next_size_limit":10,
	"event":[ "file_done", "index.html" ],
	[...] # Идентично возвращаемому значению siteInfo (см. описание далее)
}

```


---


#### dbQuery _query_
Запуск запроса к кэшу SQL

Параметр             | Описание
                 --- | ---
**query**            | Команда SQL

**Результат**: <list> Результат запроса

**Пример:**
```coffeescript
@log "Обновление информации о пользователе...", @my_address
Page.cmd "dbQuery", ["SELECT user.*, json.json_id AS data_json_id FROM user LEFT JOIN json USING(path) WHERE path='#{@my_address}/data.json'"], (res) =>
	if res.error or res.length == 0 # База данных еще не готова или пользователь не найден
		$(".head-user.visitor").css("display", "")
		$(".user_name-my").text("Посетитель")
		if cb then cb()
		return

	@my_row = res[0]
	@my_id = @my_row["user_id"]
	@my_name = @my_row["user_name"]
	@my_max_size = @my_row["max_size"]
```


---

#### fileGet _inner_path, [required]_
Получает содержимое файла

Параметр         | Описание
             --- | ---
**inner_path**   | Имя запрашиваемого файла
**required** (опционально) | Ожидание создания файла в случае его отсутствия (по умолчанию: True).

**Результат**: <string> Содержимое запрошенного файла


**Пример:**
```coffeescript
# Голосование в теме ZeroTalk
submitTopicVote: (e) =>
	if not Users.my_name # Не зарегистрирован
		Page.cmd "wrapperNotification", ["info", "Пожалуйста, получите доступ для публикации."]
		return false

	elem = $(e.currentTarget)
	elem.toggleClass("active").addClass("loading")
	inner_path = "data/users/#{Users.my_address}/data.json"

	Page.cmd "fileGet", [inner_path], (data) =>
		data = JSON.parse(data)
		data.topic_votes ?= {} # Создать в случае отсутствия
		topic_address = elem.parents(".topic").data("topic_address")

		if elem.hasClass("active") # Добавить голосование по теме
			data.topic_votes[topic_address] = 1
		else # Удаление голосование по теме
			delete data.topic_votes[topic_address]

		# Запись в файл и публикация другим пользователям
		Page.writePublish inner_path, Page.jsonEncode(data), (res) =>
			elem.removeClass("loading")
			if res == true
				@log "Файл записан"
			else # Failed
				elem.toggleClass("active") # Возвращаем состояние

	return false
```


---


#### fileDelete _inner_path_
Удаление файла

Параметр         | Описание
             --- | ---
**inner_path**   | Имя файла, подлежащего удалению

**Результат**: "ok" в случае успешного удаления или сообщение об ошибке


---


#### fileQuery _dir_inner_path, query_
Простая команда запроса файла json

Параметр             | Описание
                 --- | ---
**dir_inner_path**   | Директория запрашиваемого файла
**query**            | Команда запроса

**Результат**: <list> Удовлетворяющий запросу контент

**Пример запроса:**

 - `["data/users/*/data.json", "topics"]`: Возвращает все сообщения по теме от всех пользоваталей
 - `["data/users/*/data.json", "comments.1@2"]`: Возвращает значения `user_data["comments"]["1@2"]` от всех пользателей
 - `["data/users/*/data.json", ""]`: Возвращает все данные из файлов пользователей

**Пример:**
```coffeescript
@cmd "fileQuery", ["data/users/*/data.json", "topics"], (topics) =>
	topics.sort (a, b) -> # Сортировка по дате
		return a.added - b.added
	for topic in topics
		@log topic.topic_id, topic.inner_path, topic.title
```


---


#### fileRules _inner_path_
Получение правил для файлов

Параметр             | Описание
                 --- | ---
**inner_path**       | Путь к файлу

**Результат**: <list> Удовлетворяющий запросу контент

**Пример результата:**

```json
{
	"current_size": 2485,
	"cert_signers": {"zeroid.bit": ["1iD5ZQJMNXu43w1qLB8sfdHVKppVMduGz"]},
	"files_allowed": "data.json",
	"signers": ["1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj"],
	"user_address": "1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj",
	"max_size": 100000
}
```

**Пример:**
```coffeescript
@cmd "fileRules", "data/users/1J3rJ8ecnwH2EPYa6MrgZttBNc61ACFiCj/content.json", (rules) =>
	@log rules
```


---


#### fileWrite _inner_path, content_

Запись содержимого файла

Параметр             | Описание
             --- | ---
**inner_path**   | Путь к файлу
**content**      | Содержимое записываемого файла (в кодировке [base64](https://ru.wikipedia.org/wiki/Base64))

**Результат**: "ok" в случае успешной записи или сообщение об ошибке

**Пример:**
```coffeescript
writeData: (cb=null) ->
	# В формате json в кодировке utf8
	json_raw = unescape(encodeURIComponent(JSON.stringify({"hello": "ZeroNet"}, undefined, '\t')))
	# Кодирование в base64 и отправка
	@cmd "fileWrite", ["data.json", btoa(json_raw)], (res) =>
		if res == "ok"
			if cb then cb(true)
		else
			@cmd "wrapperNotification", ["error", "File write error: #{res}"]
			if cb then cb(false)
```

_Заметка:_ для записи файлов, отсутствующих в `content.json`, вы должны указать параметр `"own": true` в `data/sites.json` на сайте, в который вы ходите записать


---



#### serverInfo

**Return:** <dict> All information about the server

**Example:**
```coffeescript
@cmd "serverInfo", {}, (server_info) =>
	@log "Server info:", server_info
```

**Example return value:**
```json
{
	"debug": true, # Running in debug mode
	"fileserver_ip": "*", # Fileserver binded to
	"fileserver_port": 15441, # FileServer port
	"ip_external": true, # Active of passive mode
	"platform": "win32", # Operating system
	"ui_ip": "127.0.0.1", # UiServer binded to
	"ui_port": 43110, # UiServer port (Web)
	"version": "0.2.5" # Version
}
```




---


#### siteInfo

**Return**: <dict> All information about the site

**Example:**
```coffeescript
@cmd "siteInfo", {}, (site_info) =>
	@log "Site info:", site_info
```

**Example return value:**
```json
{
	"tasks": 0, # Number of files currently under download
	"size_limit": 10, # Current site size limit in MB
	"address": "1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr", # Site address
	"next_size_limit": 10, # Size limit required by sum of site's files
	"auth_address": "2D6xXUmCVAXGrbVUGRRJdS4j1hif1EMfae", # Current user's bitcoin address
	"auth_key_sha512": "269a0f4c1e0c697b9d56ccffd9a9748098e51acc5d2807adc15a587779be13cf", # Deprecated, dont use
	"peers": 14, # Peers of site
	"auth_key": "pOBdl00EJ29Ad8OmVIc763k4", # Deprecated, dont use
	"settings":  {
		"peers": 13, # Saved peers num for sorting
		"serving": true, # Site enabled
		"modified": 1425344149.365, # Last modification time of all site's files
		"own": true, # Own site
		"permissions": ["ADMIN"], # Site's permission
		"size": 342165 # Site total size in bytes
	},
	"bad_files": 0, # Files that needs to be download
	"workers": 0, # Current concurent downloads
	"content": { # Root content.json
		"files": 12, # Number of file, detailed file info removed to reduce data transfer and parse time
		"description": "This site",
		"title": "ZeroHello",
		"signs_required": 1,
		"modified": 1425344149.365,
		"ignore": "(js|css)/(?!all.(js|css))",
		"signers_sign": null,
		"address": "1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr",
		"zeronet_version": "0.2.5",
		"includes": 0
	},
	"cert_user_id": "zeronetuser@zeroid.bit", # Currently selected certificate for the site
	"started_task_num": 1, # Last number of files downloaded
	"content_updated": 1426008289.71 # Content.json update time
}
```


---


#### sitePublish _[privatekey], [inner_path], [sign]_
Publish a content.json of the site

Parameter                 | Description
                      --- | ---
**privatekey** (optional) | Private key used for signing (default: current user's privatekey)
**inner_path** (optional) | Inner path of the content json you want to publish (default: content.json)
**sign** (optional)       | If True then sign the content.json before publish (default: True)

**Return**: "ok" on success else the error message

**Example:**
```coffeescript
# Prompt the private key
@cmd "wrapperPrompt", ["Enter your private key:", "password"], (privatekey) =>
	$(".publishbar .button").addClass("loading")
	# Send sign content.json and publish request to server
	@cmd "sitePublish", [privatekey], (res) =>
		$(".publishbar .button").removeClass("loading")
		@log "Publish result:", res
```


---


#### siteSign _[privatekey], [inner_path]_
Sign a content.json of the site

Parameter                 | Description
                      --- | ---
**privatekey** (optional) | Private key used for signing (default: current user's privatekey)
**inner_path** (optional) | Inner path of the content json you want to sign (default: content.json)

**Return**: "ok" on success else the error message

> __Note:__
> Use "stored" as privatekey if its definied in users.json (eg. cloned sites)

**Example:**
```coffeescript
if @site_info["privatekey"] # Private key stored in users.json
	@cmd "siteSign", ["stored", "content.json"], (res) =>
		@log "Sign result", res
```


---



#### siteUpdate _address_

Force check and download changed content from other peers (only necessary if user is in passive mode and using old version of Zeronet)

Parameter     | Description
          --- | ---
**address**   | Address of site want to update (only current site allowed without site ADMIN permission)

**Return:** None

**Example:**
```coffeescript
# Manual site update for passive connections
updateSite: =>
	$("#passive_error a").addClass("loading").removeClassLater("loading", 1000)
	@log "Updating site..."
	@cmd "siteUpdate", {"address": @site_info.address}
```


---


# Plugin: CryptMessage


#### userPublickey _[index]_

Get user's site specific publickey

Parameter            | Description
                 --- | ---
**index** (optional) | Sub-publickey within site (default: 0)


**Return**: base64 encoded publickey

---

#### eciesEncrypt _text, [publickey], [return_aes_key]_

Encrypt a text using a publickey

Parameter                      | Description
                           --- | ---
**text**                       | Text to encrypt
**publickey** (optional)       | User's publickey index (int) or base64 encoded publickey (default: 0)
**return_aes_key** (optional)  | Get the AES key used in encryption (default: False)


**Return**: Encrypted text in base64 format or [Encrypted text in base64 format, AES key in base64 format]

---

#### eciesDecrypt _params, [privatekey]_

Try to decrypt list of texts

Parameter                      | Description
                           --- | ---
**params**                     | A text or list of encrypted texts
**privatekey** (optional)      | User's privatekey index (int) or base64 encoded privatekey (default: 0)


**Return**: Decrypted text or list of decrypted texts (null for failed decodings)

---

#### aesEncrypt _text, [key], [iv]_

Encrypt a text using the key and the iv

Parameter                      | Description
                           --- | ---
**text**                       | A text encrypt using AES
**key** (optional)             | Base64 encoded password (default: generate new)
**iv** (optional)              | Base64 encoded iv (default: generate new)


**Return**: [base64 encoded key, base64 encoded iv, base64 encoded encrypted text]


---

#### aesDecrypt _iv, encrypted_text, key_
#### aesDecrypt _encrypted_texts, keys_

Decrypt text using the IV and AES key

Parameter                      | Description
                           --- | ---
**iv**                         | IV in Base64 format
**encrypted_text**             | Encrypted text in Base64 format
**encrypted_texts**            | List of [base64 encoded iv, base64 encoded encrypted text] pairs
**key**                        | Base64 encoded password for the text
**keys**                       | Keys for decoding (tries every one for every pairs)


**Return**: Decoded text or list of decoded texts


---


# Plugin: Newsfeed


#### feedFollow _feeds_

Set followed sql queries.

The SQL query should result in rows with cols:

Field          | Description
           --- | ---
**type**       | Type: post, article, comment, mention
**date_added** | Event time
**title**      | Event's first line to be displayed
**body**       | Event's second and third line
**url**        | Link to event's page

Parameter      | Description
           --- | ---
**feeds**      | Format: {"query name": [SQL query, [param1, param2, ...], ...}, parameters will be escaped, joined by `,` inserted in place of `:params` in the Sql query.

**Return**: ok

**Example:**
```coffeescript
# Follow ZeroBlog posts
query = "
	SELECT
	 post_id AS event_uri,
	 'post' AS type,
	 date_published AS date_added,
	 title AS title,
	 body AS body,
	 '?Post:' || post_id AS url
	FROM post
"
params = [""]
Page.cmd feedFollow [{"Posts": [query, params]}]
```

---

#### feedListFollow

Return of current followed feeds


**Return**: The currently followed feeds in the same format as in the feedFollow commands


---

#### feedQuery

Execute all followed sql query


**Return**: The result of the followed Sql queries


---

# Plugin: MergerSite


#### mergerSiteAdd _addresses_

Start downloading new merger site(s)

Parameter            | Description
                 --- | ---
**addresses**         | Site address or list of site addresses


---

#### mergerSiteDelete _address_

Stop seeding and delete a merged site

Parameter            | Description
                 --- | ---
**address**           | Site address


---

#### mergerSiteList _[query_site_info]_

Return merged sites.

Parameter            | Description
                 --- | ---
**query_site_info**  | If True, then gives back detailed site info for merged sites

---


# Admin commands
_(requires ADMIN permission in data/sites.json)_



---


#### configSet _key, value_

Create or update an entry in ZeroNet config file. (zeronet.conf by default)


Parameter            | Description
                 --- | ---
**key**              | Configuration entry name
**value**            | Configuration entry new value


**Return**: ok


---



#### certSet _domain_

Set the used certificate for current site.

Parameter            | Description
                 --- | ---
**domain**           | Domain of the certificate issuer

**Return**: None


---


#### channelJoinAllsite _channel_

Request notifications about every site's events.

Parameter           | Description
               ---  | ---
**channel**         | Channel to join (see channelJoin)

**Return**: None




---


#### serverPortcheck

Start checking if port is opened

**Return**: True (port opened) or False (port closed)


---


#### serverShutdown

Stop running ZeroNet client.

**Return**: None



---


#### serverUpdate

Re-download ZeroNet from github.

**Return**: None


---


#### siteClone _address_
Copy site files into a new one.

Every file and directory will be skipped if it has a `-default` subfixed version and the subfixed version will be copied instead of it.


Eg. If you have a `data` and a `data-default` directory: The `data` directory will not be copied and the `data-default` directory will be renamed to data.

Parameter           | Description
               ---  | ---
**address**         | Address of site want to clone

**Return**: None, automatically redirects to new site on completion


---


#### siteList

**Return**: <list> SiteInfo list of all downloaded sites


---


#### sitePause _address_
Pause site serving

Parameter           | Description
               ---  | ---
**address**         | Address of site want to pause

**Return**: None


---


#### siteResume _address_
Resume site serving

Parameter           | Description
               ---  | ---
**address**         | Address of site want to resume

**Return**: None


