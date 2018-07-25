h1
====

h2 nginx файл 
----
```
server {
    listen 80;
    server_name flower.domain.com;

    location / {
        proxy_pass http://stik.one:port;
    	proxy_set_header Host $host;            
    }
}
```
h2 unit файл
----
```
[Unit]
Description=flower daemon
After=network.target

[Service]
User=django
Group=django
WorkingDirectory=/path/to/project
ExecStart=/path/to/venv/venv/bin/flower -A ProjectName --port=port --broker=redis://localhost:6379/0
[Install]
WantedBy=multi-user.target
```
Запустим 
```
sudo service flower start
sudo service flower enable
```
Done!