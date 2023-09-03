# Kittygram — социальная сеть для обмена фотографиями котиков.

## О проекте

Проект реализует веб сайт kittigram.
Kittygram - это социальная сеть, для любителей кошек. Пользователи могут добавлять фотографии своих котов и смотреть на котов, добавленных другими пользователями.
В проекте настроены Github Actions для автотестов и деплоя на сервер.

### Деплой на сервере

1. Подключитесь к удаленному серверу
2. Создайте на сервере директорию `kittygram`:
3. Установите Docker Compose на сервер:
4. Скопируйте файлы `docker-compose.production.yml` и `.env` в директорию `kittygram/` на сервере:
5. Выполните миграции, соберите статические файлы бэкенда и скопируйте их в `/backend_static/static/`:

    ```
    sudo docker-compose -f /home/yc-user/kittygram/docker-compose.production.yml exec backend python manage.py migrate
    sudo docker-compose -f /home/yc-user/kittygram/docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker-compose -f /home/yc-user/kittygram/docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
    ```

6. Откройте конфигурационный файл Nginx в редакторе nano:

    ```
    sudo nano /etc/nginx/sites-enabled/default
    ```

7. Измените настройки `location` в секции `server`:

    ```
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }
    ```

8. Перезагрузите конфиг Nginx:

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

    TELEGRAM_TO                    # ID вашего телеграм-аккаунта
    TELEGRAM_TOKEN                 # токен вашего бота
    ```