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
  - на сервере должен быть установлен Git. Для проверки выполнить `sudo apt update` `git --version`
  - если Git не установлен - установить командой `sudo apt install git`
  - находясь на сервере сгенерировать пару SSH-ключей командой `ssh-keygen`
  - сохранить открытый ключ в вашем аккаунте на GitHub. Для этого вывести ключ в терминал командой `cat .ssh/id_rsa.pub`. Скопировать ключ от символов ssh-rsa, включительно, и до конца. Добавить это ключ к вашему         аккаунту на GitHub.
  - клонировать проект с GitHub на сервер: `git clone git@github.com:Ваш_аккаунт/<Имя проекта>.git`



