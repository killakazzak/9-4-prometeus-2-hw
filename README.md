# Домашнее задание к занятию "`Prometheus-2`" - `Тен Денис`


### Задание 1*
Создайте файл с правилом оповещения, как в лекции, и добавьте его в конфиг Prometheus.

### Требования к результату
- [ ] Погасите node exporter, стоящий на мониторинге, и прикрепите скриншот раздела оповещений Prometheus, где оповещение будет в статусе Pending


Скачиваем и распаковывываем последнюю версию alertmanager
```
wget https://github.com/prometheus/alertmanager/releases/download/v0.27.0/alertmanager-0.27.0.linux-386.tar.gz
tar xvfz alertmanager-0.27.0.linux-386.tar.gz
cd alertmanager-0.27.0.linux-386
```
Устанавливаем Alertmanager
```
cp alertmanager /usr/local/bin/
cp amtool /usr/local/bin/
cp alertmanager.yml /etc/prometheus/
chown -R prometheus:prometheus /etc/prometheus/alertmanager.yml
```
Создаем сервис
```
vim /etc/systemd/system/prometheus-alertmanager.service
[Unit]
Description=Alertmanager Service Denis Ten
After=network.target
[Service]
EnvironmentFile=-/etc/default/alertmanager
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/alertmanager --config.file=/etc/prometheus/alertmanager.yml --storage.path=/var/lib/prometheus/alertmanager $ARGS
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
[Install]
WantedBy=multi-user.target
```
Запускаем и проверяем службу
systemctl daemon-reload
systemctl status  prometheus-alertmanager.service
![image](https://github.com/killakazzak/hw-prometeus-02/assets/32342205/9ac056f7-8e23-4034-8ef1-f21145893809)



---

### Задание 2*
Установите Alertmanager и интегрируйте его с Prometheus.

### Требования к результату
- [ ] Прикрепите скриншот Alerts из Prometheus, где правило оповещения будет в статусе Fireing, и скриншот из Alertmanager, где будет видно действующее правило оповещения

---

### Задание 3*

Активируйте экспортёр метрик в Docker и подключите его к Prometheus.

### Требования к результату
- [ ] приложите скриншот браузера с открытым эндпоинтом, а также скриншот списка таргетов из интерфейса Prometheus.*

---

### Задание 4* со звездочкой 

Создайте свой дашборд Grafana с различными метриками Docker и сервера, на котором он стоит.

### Требования к результату
- [ ] Приложите скриншот, на котором будет дашборд Grafana с действующей метрикой


