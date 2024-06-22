[![Main Taski workflow](https://github.com/nepa27/kittygram_final/actions/workflows/main.yml/badge.svg)](https://github.com/nepa27/kittygram_final/actions/workflows/main.yml)
# Проект Kittygram
Проект Kittygram
## Описание
Вы любитель кошек или просто ищете способ поднять себе настроение? Тогда вы попали в правильное место! Kittygram - это платформа социальных медиа, созданная для того, чтобы приносить радость и удовольствие пользователям всех возрастов. Наша миссия - создать сообщество, где любители кошек могут делиться и открывать для себя очаровательными фотографиями кошек, общаться с единомышленниками и распространять позитив и счастье.

Вы можете зарегистрироваться на сайте, а затем делиться с другими пользователями фотографиями и достижениями своих любимцев.
## Основные особенности
- Аутентификация пользователей: Создавайте аккаунты или входите, чтобы получить доступ к платформе.
- Загрузка изображений: Делитесь фотографиями кошек с другими пользователями.
- Поиск и исследование: Находите и открывайте новые фотографии кошек.
## Стек использованных технологий
+ Django 3.2
+ JWT
+ Python 3.9
+ DRF
+ PostgreSQL 13.10
+ Docker

## Запуск проекта
### Локальное развертывание

Чтобы развернуть приложение Kittygram локально:

1. Клонируйте репозиторий на вашем локальном компьютере:

```
   git clone https://github.com/nepa27/kittygram_final
   cd kittygram_final
```
   
2. Установите и активируйте виртуальное окружение c учетом версии Python 3.9:
* Если у вас Linux/macOS

```
    python3 -m venv env
    source env/bin/activate
```

* Если у вас Windows

```
    python -m venv venv
    source venv/Scripts/activate
```

+ Обновите менеджер пакетов pip:

```
python -m pip install --upgrade pip
```

+ Затем установите зависимости из файла requirements.txt:

```
cd backend
pip install -r requirements.txt
```

+ Переходим в репозиторий с manage.py и выполняем миграции:

```
python manage.py migrate
```

3. Установите зависимости для бэкенда и запустите сервер разработки:
```
   cd ../frontend
   npm install
   npm start
```
Откройте веб-браузер и перейдите по адресу http://127.0.0.1, чтобы взаимодействовать с локальной версией Kittygram.


### Удаленное развертывание
1. Подключитесь к удаленному серверу

    ```
    ssh -i PATH_TO_SSH_KEY/SSH_KEY_NAME YOUR_USERNAME@SERVER_IP_ADDRESS 
    ```

2. Создайте на сервере директорию `kittygram`:

    ```
    mkdir kittygram
    ```

3. Установите Docker Compose на сервер:

    ```
    sudo apt update
    sudo apt install curl
    curl -fsSL https://get.docker.com -o get-docker.sh
    sudo sh get-docker.sh
    sudo apt install docker-compose
    ```

4. Скопируйте файлы `docker-compose.production.yml` и `.env` в директорию `kittygram/` на сервере:

    ```
    scp -i PATH_TO_SSH_KEY/SSH_KEY_NAME docker-compose.production.yml YOUR_USERNAME@SERVER_IP_ADDRESS:/home/YOUR_USERNAME/kittygram/docker-compose.production.yml
    ```
    
    Где:
    - `PATH_TO_SSH_KEY` - путь к файлу с вашим SSH-ключом
    - `SSH_KEY_NAME` - имя файла с вашим SSH-ключом
    - `YOUR_USERNAME` - ваше имя пользователя на сервере
    - `SERVER_IP_ADDRESS` - IP-адрес вашего сервера

5. Запустите Docker Compose в режиме демона:

    ```
    sudo docker-compose -f /home/YOUR_USERNAME/kittygram/docker-compose.production.yml up -d
    ```

6. Выполните миграции, соберите статические файлы бэкенда и скопируйте их в `/backend_static/static/`:

    ```
    sudo docker-compose -f /home/YOUR_USERNAME/kittygram/docker-compose.production.yml exec backend python manage.py migrate
    sudo docker-compose -f /home/YOUR_USERNAME/kittygram/docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker-compose -f /home/YOUR_USERNAME/kittygram/docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
    ```

7. Откройте конфигурационный файл Nginx в редакторе nano:

    ```
    sudo nano /etc/nginx/sites-enabled/default
    ```

8. Измените настройки `location` в секции `server`:

    ```
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }
    ```

9. Проверьте правильность конфигурации Nginx:

    ```
    sudo nginx -t
    ```

    Если вы получаете следующий ответ, значит, ошибок нет:

    ```
    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    nginx: configuration file /etc/nginx/nginx.conf test is successful
    ```

10. Перезапустите Nginx:

    ```
    sudo service nginx reload
    ```

### Настройка CI/CD

1. Файл workflow уже написан и находится в директории:

    ```
    kittygram/.github/workflows/main.yml
    ```

2. Для адаптации его к вашему серверу добавьте секреты в GitHub Actions:

    ```
    DOCKER_USERNAME                # имя пользователя в DockerHub
    DOCKER_PASSWORD                # пароль пользователя в DockerHub
    HOST                           # IP-адрес сервера
    USER                           # имя пользователя
    SSH_KEY                        # содержимое приватного SSH-ключа (cat ~/.ssh/id_rsa)
    SSH_PASSPHRASE                 # пароль для SSH-ключа

    TELEGRAM_TO                    # ID вашего телеграм-аккаунта (можно узнать у @userinfobot, команда /start)
    TELEGRAM_TOKEN                 # токен вашего бота (получить токен можно у @BotFather, команда /token, имя бота)
    ```

### Для успешного развертывания проекта необходимо в главной директории создать файл .env, где будут указаны следуюшие параметры:

- POSTGRES_USER=kittygram_user
- POSTGRES_PASSWORD=kittygram_password
- POSTGRES_DB=kittygram
- DB_NAME=kittygram
- DB_HOST=db
- DB_PORT=5432
- SECRET_KEY=somesecretkey
- ALLOWED_HOSTS=127.0.0.1,localhost,ваш_доменный_адрес,ваш_IP_адрес
- DEBUG=False
- USE_SQLITE=False (параметр для быстрой смены БД с PostgreSQL на SQLite)
## Автор

+ [Александр Непочатых](https://github.com/nepa27) 
