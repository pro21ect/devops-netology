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
```
2. Метрики для ЦП
cpu Предоставляет статистику процессора
cpufreq Предоставляет статистику частоты процессора
- Метрики для диска
diskstats - Статистика ввода вывода
filesystem - Предоставляет статистику файловой системы, такую как используемое дисковое пространство.
- Сетевая статистика
netstat -показывает сетевую статистику
- ОП
meminfo - статистика оперативной памяти
```
```
3. Метрик собирается большое кол-во. Все перечислять не имеет смысла.
Метрики сгруппированы по группам ЦП, ОП, Дисковые, Сетевые, использование ресуросв sysmemd сервисами,
использование ресурсов группами пользователей и пр.
Из особенностей установки можно отметить после добавления строки из задания в  конфиг файл Vagrant необходима команда
vagrant reload --provision
Также после изменения конфига netdata команда
sudo systemctl restart netdata
```
```
4. Да возможно.
sudo dmesg | grep "Hypervisor detected"
Hypervisor detected: KVM
```
```
5. sysctl - утилита для упарвления параметрами ядра в реальном режиме времени
Это означает максимальное количество файловых дескрипторов, которое может выделить процесс
```