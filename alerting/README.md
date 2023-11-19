# Alerting via Gmail from Prometheus

1. Download alert manager and configure 
```
wget https://github.com/prometheus/alertmanager/releases/download/v0.16.2/alertmanager-0.16.2.linux-amd64.tar.gz
tar -xvzf alertmanager-0.16.2.linux-amd64.tar.gz
```

2. Configure alertmanager with recievers information
```
mv alertmanager-0.16.2.linux-amd64/alertmanager /usr/local/bin/
mkdir /etc/alertmanager/
vi /etc/alertmanager/alertmanager.yml
```

3. Setup alert rules and alertmanager service
```
vi /etc/prometheus/alert_rules.yml
vi /etc/systemd/system/alertmanager.service
```

4. Configure prometheus to setup alertrules and targets
```
vi /etc/prometheus/prometheus.yml
```

5. Test the email alerts
```
1. Bring down the node-exporter service from one of the node
2. Check the alerts from prometheus console
```