# Что такое ZeroNet?

ZeroNet использует криптографию Bitcoin и технологии публикации данных BitTorrent для построения **децентрализованной сети, стойкой к цензуре**.

Пользователи сети могут опубликовывать в сети ZeroNet веб-сайты, содержащие статичный либо динамичный контент, на которые могут подписываться и распространять другие пользователи. Веб-сайты всегда будут в онлайне, даже если у него всего один подписчик.

Когда контент сайта обновляется владельцем, все его подписчики (прошлые посетители) получают только обновление данные.

Браузер ZeroNet распространяется со встроенной базой данных. Такое решение позволяет упростить разработку сайтов, содержащих большой объем контента. База данных синхронизируется с публикующими узлами с помощью инкрементного обновления данных.


# Зачем это?

* Мы верим в открытую, свободную и бесцензурную связь.
* Без цензуры: Не существует способа удалить опубликованные материалы.
* Отсутствие единой точки отказа: Контент остается доступным в сети пока существует хотя бы один равный подписчик.
* Невозможно прикрыть: Это не возможно, потому что сайт хранится везде. Содержимое поддерживается любым желающим пользователем.
* Быстро: ZeroNet использует технологию BitTorrent для доставки контента, которая быстрее, чем обычные веб-серверы.
* Работает в оффлайне: Вы можете получить доступ к сайту, даже если на данный момент у Вас отсутствует интернет.
* Безопасность: Подтверждение авторства контента использует ту же технологии криптографии, которая обеспечивает защиту вашего кошелек Bitcoin.

[comment]: <> (I'm unsure about the following bit. Thoughts?)
[comment]: <> (# What problem is ZeroNet solving?)

[comment]: <> (When Tim Berners-Lee created the internet, he meant for it to be free. Not surveilled nor censored. And [he is still fighting for that](http://edition.cnn.com/2014/03/12/tech/web/tim-berners-lee-web-freedom/).)

[comment]: <> (The internet is centralized mainly in two places: Content and Domain Names (URLs) are hosted and controlled by central servers. If you control the central servers (and if you are powerful enough you do) you control the network.)

[comment]: <> (**Decentralized content storage**)

[comment]: <> (ZeroNet tackles the content storage problem by giving everyone the ability to store content. Site visitors can choose to store a website on their computers, and when they do this they also help to serve the site to other users. The site is online even if only one user is hosting it.)

[comment]: <> (**Shared DNS cache**)

[comment]: <> (Site addresses on ZeroNet are cached by all network members. When you type a ZeroNet site URL on your browser this will query other peers connected to you about the site. If one of these peers happen to have the site they will send it to you, if not, they will forward your query along.)

[comment]: <> (This architecture means that when a site URL is created, as long as one peer is serving it, there is no way to take the URL down.)


# Особенности
 * Легко, работает "из коробки".
 * Безпарольная авторизация [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki): Ваша учетная запись защищена технологией криптографии Bitcoin, используемой в электронных кошельках.
 * Обновление сайтов в режиме реального времени.
 * Поддержка доменов .bit DNS-системой Namecoin.
 * Поддержка базы данных SQL: позволяет упростить разработку сайта и обсепечить более быстую загрузку страниц.
 * Анонимность: Полная поддержка сети Tor со скрытыми сервисами .onion в замен использования адресов IPv4.
 * Поддержка шифрованных соединений TLS.
 * Автоматический, открытый uPnP-порт.
 * Плагин для многопользовательской общедоступной поддержки.
 * Работает в любом браузером на любой операционной системе.


# Как это работает?

* После установки запуска ZeroNet Вы открываете сайты, доступные по адресам:
  `http://127.0.0.1:43110/{zeronet_site_address}`
  (на пример  `http://127.0.0.1:43110/1HeLLo4uzjaLetFx6NH3PMwFP3qbRbTf3D`).
* ZeroNet использует сеть BitTorrent, чтобы найти коллег, которые публикуют этот сайт и получают от них содержимое (HTML, CSS, JS ...).
* Каждый раз, посещая сайты, Вы начинаете распространять скаченный контент остальным.
* Каждый сайт содержит список используемых файлов с проверкой sha512 хэша и подписи, генерируемой с помощью секретного ключа автора сайта.
* Если владелец сайта отредактирует свой сайт, то он занова подписывает весь список и публикует его среди подписчиков.
  После того, как подписчики проверят целостность и достоверность нового контента, они скачивают обновления и распространяют их остальным.

##### [Презентация на тему технологии криптографии ZeroNet, обновления контента сайта и многопользовательского распространения сайтов &raquo;](https://docs.google.com/presentation/d/1_2qK1IuOKJ51pgBvllZ9Yu7Au2l551t3XBgyTSvilew/pub?start=false&loop=false&delayms=3000)


# Скриншоты

![Скриншот](./img/zerohello.png)

![ZeroTalk](./img/zerotalk.png)

##### [Еще скриншоты &raquo;](/using_zeronet/sample_sites/)

# Существующие ограничения

* Отсутствие поддержки torrent-технологии разделения больших файлов
* Отсутсвие сжатия и шифрования (пока) при передачи данных (используется TLS шифрование)
* Отсутсвие сайтов с ограниченным доступом

# Помощь для поддержании проекта

Bitcoin: 1QDhxQ6PraUZa21ET5fYUCPgdrwBomnFgX


### Благодарность!

* Больше информации о поддержке, обновлений и новых сайтов ZeroNet можно найти: [http://www.reddit.com/r/zeronet/](http://www.reddit.com/r/zeronet/)
* Наш официальный чат: [#zeronet @ FreeNode](https://kiwiirc.com/client/irc.freenode.net/zeronet) or on [gitter](https://gitter.im/HelloZeroNet/ZeroNet)
