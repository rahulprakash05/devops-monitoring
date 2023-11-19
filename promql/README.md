# PromQL
```
Querying metrics in Prometheus involves using PromQL (Prometheus Query   Language) to retrieve and manipulate the time-series data collected by   Prometheus.  
```

### Examples and concepts for querying metrics in Prometheus:

1. Simple Query:  
Retrieve the current value of a metric. For example, to get the current CPU usage percentage:  
```
node_cpu_seconds_total{mode="idle"} * 100
```