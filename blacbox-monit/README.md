# BlackBox exporter  
The blackbox exporter allows blackbox probing of endpoints over HTTP, HTTPS, DNS, TCP, ICMP and gRPC.  

### Configuration  
1. Download and Install the Blackbox Exporter:  

```
wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.21.0/blackbox_exporter-0.21.0.linux-amd64.tar.gz
tar -xvf blackbox_exporter-0.21.0.linux-amd64.tar.gz
cd blackbox_exporter-0.21.0.linux-amd64
```

2. Create a Configuration File for Blackbox Exporter:  
Reference: https://github.com/prometheus/blackbox_exporter#readme  
```
modules:
  http_2xx_example:
    prober: http
    timeout: 5s
    http:
      valid_http_versions: ["HTTP/1.1", "HTTP/2.0"]
      valid_status_codes: []  # Defaults to 2xx
      method: GET
```

3. Create the service file for blackbox
```
/etc/systemd/system/blackbox.service

[Unit]
Description=Prometheus BlackBox Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
Type=simple
ExecStart=/opt/blackbox_exporter-0.21.0.linux-amd64/blackbox_exporter --config.file=/opt/blackbox_exporter-0.21.0.linux-amd64/monitor_website.yml

[Install]
WantedBy=multi-user.target
```
4. Update the prometheus configuration  
```
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx_example]  # Look for a HTTP 200 response.
    static_configs:
      - targets:
        - http://www.google.com    # Target to probe with http.
        - https://youtube.com   # Target to probe with https.
        - http://52.203.9.91:8080 # Target to probe with http on port 8080.
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: localhost:9115  # The blackbox exporter's real hostname:port.
```

5. Restart the daemon and start the service
```
systemctl daemon-reload  
systemctl start blackbox  
systemctl restart prometheus  
```

### Grafana http queries
```
probe_http_status_code
probe_http_ssl
sum by(instance) (probe_httpd_ssl)
avg by(phase) (probe_http_duration_seconds)
max by(instance) (probe_ssl_earliest_cert_expiry() - time()) / (60*60*24)
curl -s "localhost:9115/probe?target=https://google.com&module=http_200_module"
```