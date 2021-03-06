# Часто задаваемые вопросы


#### Должен ли мой компьютер быть доступен из сети Интернет по открытому порту?

Это совершенно не обязательно, вы можете просматривать и использовать сайты ZeroNet без наличия открытого порта.
Все же, если вы хотите создавать сайт, мы рекомендуется иметь открытый порт.

При запуске ZeroNet пытается открыть порт на маршрутизаторе с помощью технологии [UPnP](https://wikipedia.org/wiki/Universal_Plug_and_Play),
если это не удается, вы необходимо сделать это вручную:

- Вам необходимо получить доступ к вашему роутеру через веб-интерфейс, обычный адрес в браузере [http://192.168.1.1](http://192.168.1.1)
и [http://192.168.0.1](http://192.168.0.1)
- Найдите и проверьте включение настройки "Enable UPnP support" (включить поддержку UPnP), либо подобного наименования, и перезагрузите ZeroNet.

Если все же он по-прежнему не работает, попробуйте найти раздел 'port forwarding' (перенаправления портов). Эта опция реализован по-разному у разных производителей. [Смотрите инструкцию по настройке на канале YouTube](https://www.youtube.com/watch?v=aQXJ7sLSz14). Порт должен быть перенаправляться на внутренний 15441.


---


#### Является ли ZeroNet анонимным?

Честно, ZeroNet обеспечивает анонимность не более BitTorrent. Но уровень конфиденциальности, а именно возможность выяснить владельца публикации или сайта, возрастает по мере увеличения всей сети и увеличения подписчиков сайта.

ZeroNet создан для работы с анонимными сетями: вы можете легко скрыть свой IP, используя сеть Tor.


---


#### Как можно использовать ZeroNet в браузерах Tor?

В режиме Tor рекомендуется использовать ZeroNet в соответствующих браузерах:
- Необходимо запустить браузер Tor
- Откройте с помощью адресной строки страницу `about:preferences#advanced`
- Перейдите в `Настройки ...`
- Введите значение `127.0.0.1` в поле `Отключить прокси для` (No proxy for)


---


#### Как пользоваться ZeroNet вместе с Tor?

Если вы хотите, чтобы ваш IP был скрыт, установить последнюю версию ZeroNet, затем нажмите `Tor`> `Включить Tor для каждого соединения` (Enable Tor for every connection) на ZeroHello.

В операционной системе Windows Tor устанавливается вместе с ZeroNet, для других операционных систем [используйте инструкции установки Tor](https://www.torproject.org/docs/installguide.html),
Исправьте конфигурационный файл torrc, убрав комментирование `` # из строки `# ControlPort 9051` и затем перезапустить службу Tor и ZeroNet.

> __Прим.: __ Вы можете проверить свой IP-адрес с помощью страницы ZeroNet [Статистика](http://127.0.0.1:43110/Stats).

> __Прим.: __ При ошибке подключения убедитесь, что у вас установлена последняя версия Tor (требуемая версия 0.2.7.5 и выше).


---


#### Как настроить работу ZeroNet с Tor под Linux?

Обновите Tor до последней версии (необходимая 0.2.7.5 или выше), следуйте [этим инструкциям](https://www.torproject.org/docs/debian.html.en). Например, для Debian:

 - `echo 'deb http://deb.torproject.org/torproject.org jessie main'>> /etc/apt/sources.list.d/tor.list`
 - `gpg --keyserver keys.gnupg.net --recv 886DDD89`
 - `gpg --export A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89 | apt-key add -`
 - `apt-get update`
 - `apt-get install tor`

Конфигурирование протокола управления:

 - Откройте для редактирования файл настроек torrc`mcedit /etc/tor/torrc`
 - Удалите символ `#` в строке `ControlPort 9051` и `CookieAuthentication 1` (приблизительно строка 57)
 - Перезапустите Tor `/etc/init.d/tor restart`
 - Добавьте привилегий для чтения куки (cookie) с помощью `usermod -a -G debian-tor [yourlinuxuser]` <br> (если вы используете не Debian, проверьте доступ к файлу для группы пользователей с помощью `ls -al /var/run/tor/control.authcookie`)
 - Выдите их системы и зайдите снова для применения настроек
 
> __Прим.:__ Вы можете проверить корректность запуска Tor, используя команду `echo 'PROTOCOLINFO' | nc 127.0.0.1 9051`

> __Прим.:__ Также возможна настройка без правки torrc и использования другой версии клиента Tor с помощью запуска `zeronet.py --tor disable --proxy 127.0.0.1:9050 --disable_udp`, но в таком случае Вы потеряете возможность нормально общаться с другими адресами .onion.


---

#### Могу ли я использовать одно имя пользователя на разных компьютерах?

Конечно, для этого достаточно скопировать файл `data/users.json`.


---


#### Как можно зарегистрировать домен .bit?

Вы можете зарегистрировать домены .bit с помощью [Namecoin](https://namecoin.info/).
Управление доменами возможно через графической интерфейс или с помощью [командной строки](http://www.christopherpoole.net/registering-a-bit-domain-with-namecoin.html).

После того, как регистрация завершится, Вы необходимо отредактировать запись вашего домена, добавив к нему раздел zeronet, например .:

```
{
...
    "zeronet": {
        "": "1EU1tbG9oC1A8jz2ouVwGZyQ5asrNsE4Vr",
        "blog": "1BLogC9LN4oPDcruNz3qo1ysa133E9AGg8",
        "talk": "1TaLk3zM7ZRskJvrh3ZNCDVGXvkJusPKQ"
    },
...
}
```
"" это относится к доменам верхнего уровня, а также любой всех его поддоменов.


> __Прим.:__ Есть другие возможности зарегистрировать домен .bit: [domaincoin.net](https://domaincoin.net/), [peername.com](https://peername.com/), [dotbit.me](https://dotbit.me/)

> __Прим.:__ Вы можете проверить свой домен на сайте [namecha.in](http://namecha.in/), к примеру: [zeroid.bit](http://namecha.in/name/d/zeroid)

> __Прим.:__ При регистрации в имени домена Вы можете использовать только [символы нижнего регистра, цифры и знак дефиса](http://wiki.namecoin.info/?title=Domain_Name_Specification_2.0#Valid_Domains).

> __Прим.:__ Для привязки ZeroHello к Вашему домену вместо адреса вашего сайта, добавьте ключ домена в настроечный файл content.json ([пример такой настройки](https://github.com/HelloZeroNet/ZeroBlog/blob/master/content.json#L6)).


---


#### Могу ли я использовать сгенерированный адрес сайта / закрытый ключ для приема платежей Bitcoin?

Да, это стандартный адрес Bitcoin. Закрытый ключ отформатирован в стандарте WIF, поэтому он может использоваться в большинстве клиентов.

> __Прим.: __ Не рекомендуется хранить слишком большую сумму на адресе вашего сайта, потому что Вам необходимо указывать ваш закрытый ключ каждый раз, когда вы изменили ваш сайт.


---


#### Что происходит, когда кто-то размещает в сети вредоносный контент?

Сайты ZeroNet полностью изолированы, они имеют такие же привилегии, как и любые другие посещаемые Вами веб-сайты в Интернет.
Вы имеете полный контроль над тем того, что вы загружаете. Если вы сайт показался подозрительным, Вы можете полностью его удалить в любое время.


---


#### Возможна ли установка ZeroNet к удаленной машине?
Да, вы должны включить плагин UiPassword, переименовав __plugins/disabled-UiPassword__ directory в __plugins/UiPassword__,
затем запустите ZeroNet на удаленной машине с помощью команды <br> `zeronet.py --ui_ip "*" --ui_password anypassword`.
Это позволит связать веб-сервер ZeroNet UI для любых интерфейсов, но для безопасности получить к нему доступ можно только с помощью полученного пароля.

> __Прим.: __ Вы также можете ограничить доступ к интерфейсу для определенного IP-адреса с помощью команды `--ui_restrict ip1 ip2`.

> __Прим.: __ Вы можете указать пароль в конфигурационном файле путем создания файла `zeronet.conf` и добавления в него раздела ` [global] и строки `ui_password = anypassword`.


---


#### Есть ли способ отследить пропускную способность ZeroNet?

Объем переданных/принятых байтов отображаются на боковой панели ZeroNet в <br> (откройте ее, переместив правую верхнюю кнопку '0' влево)

> __Прим.: __ Страница статистики подключения доступна по адресу: [http://127.0.0.1:43110/Stats](http://127.0.0.1:43110/Stats)


---


#### Что произойдет, если двое используют один и тот же ключ для изменения сайта?
Каждый файл content.json имеет отметку времени, поэтому клиенты всегда загружают сайты свежий.


---


#### Использует ли ZeroNet технологию Bitcoin распределенного хранения записей (blockchain)?

Нет, ZeroNet использует только криптографию Bitcoin для адресов сайтов, а также подписи и верификации контента.
Идентификация пользователей также основана на формате Bitcoin [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki).

Механизм распределенного хранения (blockchain) в настоящее время используется Namecoin для регистрации доменов.


---


#### Ограничивается ли поддержка ZeroNet только форматами HTML и CSS для веб-сайтов?

Целью ZeroNet являются динамические сайты, обновляемые в режиме реального времени, но вы можете распространять с его помощью любые файлы.
(VCS хранилища, собственный тонкий клиент, базы данных и т.д.)


---


#### Как создать сайт для ZeroNet?

[Ознакомьтесь со следующей инструкцией](/using_zeronet/create_new_site/).

---


#### Как это работает?

- Перед открытием нового сайта ZeroNet запрашивает IP-адреса посетителей из сети BitTorrent
- Сначала загружается файл с именем __content.json__, который содержит все необходимые имена остальных файлов,
  __значения хеш__ и криптографическую подпись владельца сайта
- Загруженный файл content.json __проходит проверку с помощью __адреса__ сайта и __криптографической подписи__ владелец сайта
- __Загружается следующий файл__ (html, css, js ...) и проверяется с помощью значения хэш SHA512 файла content.json
- Каждый посещенный сайт становится __публикуемый вами__ для других участников сети.
- Если владелец сайта (имеющий закрытый ключ для адреса сайта) __обновит__ контента своего сайта, то он подписывает
  новый content.json и __публикует его среди подписчиков__. После того, как подписчики проверят целостность content.json
  (с помощью криптографической подписи), они __загружают измененный файлы__ и публикуют уже обновленной содержание для других посетителей.

Дополнительная информация:
 [Описание примеров сайтов ZeroNet](/using_zeronet/sample_sites/),
 [Презентации о принципах работы сети ZeroNet](https://docs.google.com/presentation/d/1_2qK1IuOKJ51pgBvllZ9Yu7Au2l551t3XBgyTSvilew/pub)
