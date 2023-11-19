# Metric-based alerts with conditions in Prometheus

1. Create or edit your Prometheus alerting rules file. This is typically named prometheus.rules or similar.  
```
groups:
- name: example
  rules:
  - alert: HighCpuUsage
    expr: node_cpu_seconds_total{mode="idle"} < 50
    for: 5m
    labels:
      severity: critical
    annotations:
      summary: "High CPU Usage detected"
      description: "Node {{ $labels.instance }} has high CPU usage."
```
In this example:
1. expr: The PromQL expression defining the condition for the alert. In this case, it triggers when the idle CPU time is less than 50.  
2. for: It specifies how long the condition must be true before the alert fires (5 minutes in this case).  
3. labels: Additional labels attached to the alert, such as severity.  
4. annotations: Additional metadata for the alert.  

2. Configure Prometheus to use the alerting rules file. In your Prometheus configuration file (prometheus.yml), add the following:  
```
rule_files:
  - /path/to/prometheus.rules
```
Replace /path/to/prometheus.rules with the actual path to your alerting rules file.  

3. Restart Prometheus. After making changes to the configuration, restart Prometheus to apply the new settings.  

4. Check Alert Status. You can check the status of your alerts in the Prometheus web interface (http://your-prometheus-instance:9090/alerts).  


### MORE EXAMPLES HERE
1. Alert on Memory Usage:
```
- alert: HighMemoryUsage
  expr: node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100 < 20
  for: 10m
  labels:
    severity: warning
  annotations:
    summary: "High Memory Usage"
    description: "Node {{ $labels.instance }} is running low on memory."
```

2. Alert on Disk Space Usage:
```
- alert: LowDiskSpace
  expr: (node_filesystem_size_bytes - node_filesystem_free_bytes) / node_filesystem_size_bytes * 100 > 90
  for: 15m
  labels:
    severity: critical
  annotations:
    summary: "Low Disk Space"
    description: "Disk space on {{ $labels.mountpoint }} is running low."
```

3. Alert on HTTP 5xx Errors:
```
- alert: HTTPServerErrorRate
  expr: sum(rate(http_requests_total{status="500"}[1m])) / sum(rate(http_requests_total[1m])) * 100 > 5
  for: 5m
  labels:
    severity: critical
  annotations:
    summary: "High HTTP 5xx Error Rate"
    description: "Server is experiencing a high rate of HTTP 5xx errors."
```

4. Alert on Pod Restart Rate:
```
- alert: HighPodRestarts
  expr: sum(rate(kube_pod_container_status_restarts_total[5m])) by (pod) > 3
  for: 10m
  labels:
    severity: warning
  annotations:
    summary: "High Pod Restarts"
    description: "Pod {{ $labels.pod }} is restarting frequently."
```