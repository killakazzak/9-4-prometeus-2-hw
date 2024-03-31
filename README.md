# Домашнее задание к занятию "`Prometheus-2`" - `Тен Денис`


### Задание 1*
Создайте файл с правилом оповещения, как в лекции, и добавьте его в конфиг Prometheus.

### Требования к результату
- [ ] Погасите node exporter, стоящий на мониторинге, и прикрепите скриншот раздела оповещений Prometheus, где оповещение будет в статусе Pending

### Решение Задание 1*

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

Интегрируем Alertmanager с Prometheus
```
vim /etc/prometheus/prometheus.yml
```

```
---
global:
  scrape_interval: 15s
  evaluation_interval: 15s
alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - localhost:9093
rule_files:
  - tenda-alert.yml
scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets:
          - localhost:9090
          - localhost:9100
```

Создаем файл с правилом оповещения
```
vim /etc/prometheus/tenda-alert.yml
```
```
groups: # Список групп
- name: tenda-alert # Имя группы
  rules: # Список правил текущей группы
    - alert: InstanceDown # Название текущего правила
      expr: up == 0 # Логическое выражение
      for: 1m # Сколько ждать отбоя сработки перед отправкой оповещения
      labels:
        severity: critical # Критичность события
      annotations: # Описание
        description: '{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 1 minute.' # Полное описание алерта
        summary: Instance {{ $labels.instance }} down # Краткое описание алерта
```

Настраиваем alertmanager
```
vim /etc/prometheus/alertmanager.yml
```
```
---
global:
route:
  group_by: ['alertname'] # Параметр группировки оповещений — по имени
  group_wait: 30s # Сколько ждать восстановления, перед тем как отправить первое оповещение
  group_interval: 10m # Сколько ждать, перед тем как дослать оповещение о новых сработках по текущему алерту
  repeat_interval: 60m # Сколько ждать, перед тем как отправить повторное оповещение
  receiver: 'email' # Способ, которым будет доставляться текущее оповещение
receivers: # Настройка способов оповещения
- name: 'email'
  email_configs:
    - to: 'yourmailto@todomain.com'
      from: 'yourmailfrom@fromdomain.com'
      smarthost: 'mailserver:25'
      auth_username: 'user'
      auth_identity: 'user'
      auth_password: 'paS$w0rd'
```
Перезапускаем alert-manager

```
systemctl restart prometheus-alertmanager.service
```

Останавливаем node-exporter
```
systemctl stop node-exporter.service
```
![image](https://github.com/killakazzak/hw-prometeus-02/assets/32342205/d6d76501-870d-4937-ae35-4d4ebce1309f)


---

### Задание 2*
Установите Alertmanager и интегрируйте его с Prometheus.

### Требования к результату
- [ ] Прикрепите скриншот Alerts из Prometheus, где правило оповещения будет в статусе Fireing, и скриншот из Alertmanager, где будет видно действующее правило оповещения

### Решение Задание 2*


![image](https://github.com/killakazzak/hw-prometeus-02/assets/32342205/b1eaab10-1018-4f81-8633-873c314f0aea)

![image](https://github.com/killakazzak/hw-prometeus-02/assets/32342205/719354c6-5135-4df0-a17b-e4923d108eb1)

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


