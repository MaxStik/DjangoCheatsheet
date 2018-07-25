Redis
=====
Переходим во временную папку
`cd /tmp`
Качаем, распаковываем, переходим
```curl -O http://download.redis.io/redis-stable.tar.gz
tar xzvf redis-stable.tar.gz
cd redis-stable
```
Собираем, тестируем, Устанавливаем
```
make
make test
make install
```
Создаем папку редиса в etc, копируем конфиг и меняем под себя
```
sudo mkdir /etc/redis
sudo cp /tmp/redis-stable/redis.conf /etc/redis
sudo nano /etc/redis/redis.conf
```
### Настраиваем
значение `supervised` задаем `systemd`
значение `dir` ставим `/var/lib/redis`

Создаем юнит-файл
`sudo nano /etc/systemd/system/redis.service`

```
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
User=redis
Group=redis
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
```

Создаем пользователя redis и даем права
```
sudo adduser --system --group --no-create-home redis
sudo mkdir /var/lib/redis
sudo chown redis:redis /var/lib/redis
sudo chmod 770 /var/lib/redis
```

Запускаем юнит
```
sudo systemctl start redis
sudo systemctl enable redis
```
#### Все!
