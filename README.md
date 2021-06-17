```
1. Качаю архив node_exporter
- curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.1.2/node_exporter-1.1.2.linux-386.tar.gz
- Распоковываю
- tar xvf node_exporter-1.1.2.linux-386.tar.gz
- Перехожу в созданную папку
- Копирую исполняемый файл /usr/local/bin
- sudo cp node_exporter /usr/local/bin
- Создаю пользователя под node_exporter без возможности авторизации
- sudo useradd --no-create-home --shell /bin/false node_exporter
- Передаю права пользователю node_exporter
- sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
- Создаю служебный файл systemd
- sudo nano /etc/systemd/system/node_exporter.service
- Заполняю его
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter
EnvironmentFile=-/etc/default/node_exporter $OPTIONS

[Install]
WantedBy=multi-user.target
- Создаю файл /etc/default/node_exporter
vim /etc/default/node_exporter
- Заполняю
OPTIONS="--collector.disable-defaults --collector.cpu --collector.meminfo --collector.filesystem --collector.netdev"
- перезапускаю systemd
-sudo systemctl daemon-reload
- Стартую
sudo systemctl start node_exporter
- Проверяю
sudo systemctl status node_exporter
-Добавляю в автозапуск
sudo systemctl enable node_exporter
- Проверяю ещё
curl http://localhost:9100/metrics
```
   