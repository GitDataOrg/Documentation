# Установка ZeroNet

* Загрузите пакет ZeroBundle своей операционной системы: [Microsoft Windows](https://github.com/HelloZeroNet/ZeroBundle/releases/download/0.1.1/ZeroBundle-v0.1.1.zip), [Apple OS X](https://github.com/HelloZeroNet/ZeroBundle/releases/download/0.1.1/ZeroBundle-mac-v0.1.1.zip), [Linux 64bit](https://github.com/HelloZeroNet/ZeroBundle/releases/download/0.1.1/ZeroBundle-linux64-v0.1.1.tar.gz), [Linux 32bit](https://github.com/HelloZeroNet/ZeroBundle/releases/download/0.1.1/ZeroBundle-linux32-v0.1.1.tar.gz);
* Разрахивируйте в любом месте;
* Запустите `ZeroNet.cmd` (для Windows), `ZeroNet(.app)` (для OS X), `ZeroNet.sh` (для Linux).

После запуска будет загружена и автоматически запущена последняя версия ZeroNet.

#### Ручная установка 
#### [Debian Linux](https://www.debian.org/)

* `sudo apt-get update`
* `sudo apt-get install msgpack-python python-gevent`
* `wget https://github.com/HelloZeroNet/ZeroNet/archive/master.tar.gz`
* `tar xvpfz master.tar.gz`
* `cd ZeroNet-master`
* Выполните команду `python zeronet.py`
* Откройте страницу http://127.0.0.1:43110/ в браузере

### [Vagrant](https://www.vagrantup.com/)

* `vagrant up`
* Подключитесь к VM `vagrant ssh`
* `cd /vagrant`
* Выполните команду `python zeronet.py --ui_ip 0.0.0.0`
* Откройте страницу http://127.0.0.1:43110/ в браузере

### [Docker](https://www.docker.com/)
* `docker run -d -v <local_data_folder>:/root/data -p 15441:15441 -p 43110:43110 nofish/zeronet`
* Откройте страницу http://127.0.0.1:43110/ в браузере

### [Virtualenv](https://virtualenv.readthedocs.org/en/latest/)

* `virtualenv env`
* `source env/bin/activate`
* `pip install msgpack-python gevent`
* `python zeronet.py`
* Откройте страницу http://127.0.0.1:43110/ в браузере
 
