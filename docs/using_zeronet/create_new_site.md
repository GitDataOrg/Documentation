# Создание сайта ZeroNet


### 1. Подготовка структуры сайта

* Если ZeroNet запущен, остановите его
* Откройте папку установки ZeroNet и выполните команду:

```bash
$ zeronet.py siteCreate
...
- Site private key: 23DKQpzxhbVBrAtvLEc2uvk7DZweh4qL3fn3jpM3LgHDczMK2TtYUq
- Site address: 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2
...
- Site created!
$ zeronet.py
...
```

- Эта команда подготовит исходные файлы для сайта в папке ```data/13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2```. Сохраните сгенерированный секретный (private key) и запомните идентификатор (адрес) сайта, они вам понадобятся в будущем неоднократно.

> __Замечание:__
> Пользователям Windows, использующие пакетную (bundle) версию, необходимой перейти в папку ZeroBundle/ZeroNet и выполнить `"../Python/python.exe" zeronet.py siteCreate`

### 2. Создание и изменение сайта

* Обновите файлы сайта, расположенные в папке ```data/[ключ вашего сайта]``` (на пример: 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2).
* Когда сайт будет готов для запуска, выполните:

```bash
$ zeronet.py siteSign 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2
- Signing site: 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2...
Private key (input hidden):
```

* Введите секретный ключ, полученный при создании сайта. This will sign all files so peers can verify that the site owner is who made the changes.

### 3. Публикация изменений

* Для оповещения пользователей о новой редации сайта вам необходимо выполнить команду:

```bash
$ zeronet.py sitePublish 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2
...
Site:13DNDk..bhC2 Publishing to 3/10 peers...
Site:13DNDk..bhC2 Successfuly published to 3 peers
- Serving files....
```

* Вот и все! Сейчас вы успешно подписали и опубликовали обвновления сайта.
* Ваш теперь доступен по адресу: ```http://localhost:43110/13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2```


**Сделующий шаг:** [Документация разработчика ZeroNet](/site_development/getting_started/)
