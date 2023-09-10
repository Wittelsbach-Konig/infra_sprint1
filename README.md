![Gunicorn](https://img.shields.io/badge/gunicorn-%298729.svg?style=for-the-badge&logo=gunicorn&logoColor=white)
![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![Django](https://img.shields.io/badge/django-%23092E20.svg?style=for-the-badge&logo=django&logoColor=white)
![DjangoREST](https://img.shields.io/badge/DJANGO-REST-ff1709?style=for-the-badge&logo=django&logoColor=white&color=ff1709&labelColor=gray)
<br>
# Kittygram
Kittygram - социальная сеть для выкладывания фотографий своих пушистых питомцев.

## Деплой проекта на локальный компьютер:
 - Клонировать репозиторий `git clone <адрес вашего репозитория>`
 - перейти в директорию с клонированным репозиторием
 - установить виртуальное окружение `python3 -m venv venv`
 - установить зависимости `pip install -r requirements.txt`
 - в директории /backend/kittygram_backend/ создать файл .env
 - в файле .env прописать необходимые поля (смотри .env.example)

## Деплой проекта на удалённый сервер:
 ### Подключение сервера к аккаунту на GitHub
  - на сервере должен быть установлен Git. Для проверки выполнить в терминале `git --version`
  - если Git не установлен - установить командой `sudo apt install git`
  - находясь на сервере сгенерировать пару SSH-ключей командой `ssh-keygen`
  - сохранить открытый ключ в вашем аккаунте на GitHub. Для этого вывести ключ в терминал командой `cat .ssh/id_rsa.pub`. Скопировать ключ от символов ssh-rsa, включительно, и до конца. Добавить это ключ к вашему     аккаунту на GitHub.
  - клонировать проект с GitHub на сервер: `git clone git@github.com:Ваш_аккаунт/<Имя проекта>.git`
 ### Подготовка backend части проекта
  - установить пакетный менеджер и утилиту для создания виртуального окружения `sudo apt install python3-pip python3-venv -y`
  - находясь в директории с проектом создать и активировать виртуальное окружение `python3 -m venv venv`  `source venv/bin/activate` 
  - установить зависимости `pip install -r requirements.txt`
  - выполнить миграции `python manage.py migrate`
  - создать суперюзера `python manage.py createsuperuser`
  - создать файл .env в директории /backend/kittygram_backend/, прописать необходимые поля (смотри .env.example)
  - в файле _settings.py_ прописать настройки 
  ```python
  STATIC_URL = 'static_backend'
  STATIC_ROOT = BASE_DIR / 'static_backend'
  ```
  - активировать виртуальное окружение проекта, перейти в директорию с файлом _manage.py_ и выполнить команду `python manage.py collectstatic`
  - в директории_<имя_проекта>/backend/_ будет создана директория _static_backend/_ 
  - Скопировать директорию _static_backend/_ в директорию _/var/www/<имя_проекта>/_
 ### Подготовка frontend части проекта
  - установить на сервер `Node.js`   командами
   ```shell
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\
   sudo apt-get install -y nodejs
   ```
  - установить зависимости frontend приложения. Из директории `<ваш проект>/frontend/` выполнить команду: `npm i`
  - Перейти в директорию _<имя_проекта>/frontend/_  и выполнить команду `npm run build` Результат сохранится в директории ..._/frontend/build/_.  В системную директорию сервера _/var/www/_ скопировать содержимое      папки _/frontend/build/_
 ### Установка и запуск Gunicorn
  - при активированном виртуальном окружении проекта установить пакет gunicorn `pip install gunicorn==20.1.0`
  - В директории _/etc/systemd/system/_ создайте файл _gunicorn.service_ `sudo nano /etc/systemd/system/gunicorn.service`  со следующим кодом :
   ```
    [Unit]

    Description=gunicorn daemon

    After=network.target

    [Service]

    User=yc-user

    WorkingDirectory=/home/<имя пользователя в системе>/<имя проекта>/backend/

    ExecStart=/home/<имя пользователя в системе>/<имя проекта>/venv/bin/gunicorn --bind 0.0.0.0:8080 kittygram_backend.wsgi

    [Install]

    WantedBy=multi-user.target
   ```
   Чтобы точно узнать путь до Gunicorn можно при активированном виртуальном окружении использовать команду `which gunicorn`
 ### Установка и настройка Nginx
  - На сервере из любой директории выполнить команду: `sudo apt install nginx -y`
  - Для установки ограничений на открытые порты выполнить по очереди команды: `sudo ufw allow 'Nginx Full'`  `sudo ufw allow OpenSSH`
  - включить файервол `sudo ufw enable`  
  - открыть файл конфигурации веб-сервера `sudo nano /etc/nginx/sites-enabled/default` и заменить его содержимое следующим кодом:
  ```
    server {
	listen 80;
	server_name <публичный_ip_вашего_удаленного_сервера> <ваш-домен>;

	location /api/ {
	    proxy_pass http://127.0.0.1:8080;
     client_max_body_size 20M;
	}
	       location /admin/ {
			  proxy_pass http://127.0.0.1:8080;
     client_max_body_size 20M;
			     }
	location / {
	    root   /var/www/<имя_проекта>;
	    index  index.html index.htm;
	    try_files $uri /index.html;
	}

    }
  ```
  - Сохранить изменения, проверить и перезагрузить конфигурацию веб-сервера:
  ```shell
  sudo nginx -t
  sudo systemctl reload nginx
  ```
 ### Получение и настройка SSL-сертификата
  **Установка certbot**
  - Зайдите на сервер и последовательно выполните команды:
  ```shell
  sudo apt install snapd
  sudo snap install core; sudo snap refresh core
  sudo snap install --classic certbot
  sudo ln -s /snap/bin/certbot /usr/bin/certbot
  ```
  - запустить certbot и получить SSL-сертификат:
  ```shell
  sudo certbot --nginx
  ```
  - сертификат автоматически сохранится на вашем сервере в системной директории _/etc/ssl/_  Также будет автоматически изменена конфигурация Nginx: в файл _/etc/nginx/sites-enabled/default_ добавятся новые       
    настройки и будут прописаны пути к сертификату.
  - перезагрузить конфигурацию Nginx:
  ```shell
  sudo systemctl reload nginx
  ```
