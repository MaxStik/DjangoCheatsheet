h1 Деплой проекта на сервер
====

Обновляем сервер.
sudo apt-get update
Ставим системные пакеты:
sudo apt-get install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx ruby redis build-essential tcl
Логинимся в базу
sudo -u postgres psql
Создаем базу пользователя и даем ему права
```
CREATE DATABASE myproject;
CREATE USER myprojectuser WITH PASSWORD 'password';
ALTER ROLE myprojectuser SET client_encoding TO 'utf8';
ALTER ROLE myprojectuser SET default_transaction_isolation TO 'read committed';
ALTER ROLE myprojectuser SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;
```
Выходим 
`\q`
Апгрейдим pip и ставим virtualenv
```
sudo -H pip3 install --upgrade pip
sudo -H pip3 install virtualenv
```

Создаем папку проекта и переходим в нее
```
mkdir ~/myproject
cd ~/myproject
```
Создаем окружение
`virtualenv venv`
Активируем окружение
`source venv/bin/activate`
Клонируем проект с репозитория
`git clone link`
Устанавливаем все пакеты
`pip install -r requirements.txt`



h2 Gunicorn
----- 
```
pip install gunicorn
sudo nano /etc/systemd/system/project.service
```

```
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=sammy
Group=www-data
WorkingDirectory=/home/sammy/myproject
ExecStart=/home/sammy/myproject/myprojectenv/bin/gunicorn --access-logfile - --workers 3 --bind unix:/home/sammy/myproject/myproject.sock myproject.wsgi:application

[Install]
WantedBy=multi-user.target
```

h2 Nginx
----
```
server {
        listen 80;
        listen 443 ssl;
        server_name domain;

        location / {
            proxy_set_header X-Forwarded-Proto $scheme;
            include         proxy_params;
            proxy_pass http://unix:/home/django/domain/stik.sock;
            
        }

        location /static {
            alias /path/to/static/;
        }
}
```

Запускаем проект
```
sudo service project start
sudo service project enable
```