# PromQL
```
Querying metrics in Prometheus involves using PromQL (Prometheus Query Language) to retrieve and manipulate the time-series data collected by Prometheus.  
```

### Examples and concepts for querying metrics in Prometheus:

1. Simple Query:  
Retrieve the current value of a metric. For example, to get the current CPU usage percentage:  
```
node_cpu_seconds_total{mode="idle"} * 100
```

2. Rate Calculation:
Calculate the rate of change of a metric over time. For instance, to get the rate of incoming network traffic:
```
rate(node_network_receive_bytes_total[5m])  
```
This query uses the rate function to calculate the rate of change of the node_network_receive_bytes_total metric over a 5-minute window.  

3. Aggregation:
Aggregate data over a time range. For example, to calculate the average CPU usage over the last 5 minutes:
```
avg_over_time(node_cpu_seconds_total{mode="idle"}[5m]) * 100
```
This query uses the avg_over_time function to calculate the average CPU usage over a 5-minute window.

4. Filtering with Labels:
Use labels to filter metrics. For instance, to get the memory usage of a specific node:
```
node_memory_MemTotal_bytes{instance="your_node_ip"} - (node_memory_MemFree_bytes{instance="your_node_ip"} + node_memory_Buffers_bytes{instance="your_node_ip"} + node_memory_Cached_bytes{instance="your_node_ip"})
```
Replace "your_node_ip" with the actual IP address or label value for your specific node.  

5. Logical Operations:
Combine metrics or perform logical operations. For example, to find nodes with high CPU usage:
```
node_cpu_seconds_total{mode="idle"} < 50
```
This query returns nodes where the idle CPU time is less than 50, indicating potentially high CPU usage.  

The node_cpu_seconds_total metric is a counter, and when comparing it to a threshold, it's advisable to calculate the rate of change using the rate() function  

```
rate(node_cpu_seconds_total{mode="idle"}[5m]) * 100 < 50
```
In this query, rate(node_cpu_seconds_total{mode="idle"}[5m]) calculates the rate of change of the node_cpu_seconds_total metric over a 5-minute window, and then * 100 is used to convert it to a percentage. The comparison is then made to check if the CPU usage is less than 50%.  


6. Top-N Metrics:
Retrieve the top N metrics based on a certain criterion. For example, to get the top 5 nodes with the highest CPU usage:  
```
topk(5, node_cpu_seconds_total{mode="idle"} * 100)
```
This query uses the topk function to retrieve the top 5 nodes with the highest CPU usage.

When using a counter metric like node_cpu_seconds_total, it's essential to calculate the rate of change using the rate() function, especially when working with the topk function.  
```
topk(5, rate(node_cpu_seconds_total{mode="idle"}[5m]) * 100)
```
In this query, rate(node_cpu_seconds_total{mode="idle"}[5m]) calculates the rate of change of the node_cpu_seconds_total metric over a 5-minute window, and then * 100 is used to convert it to a percentage. The topk function is applied to find the top 5 nodes with the highest CPU usage.  


7. Alerting Rules:
Define alerting rules based on certain conditions. For instance, to create an alert when CPU usage exceeds a threshold:
```
ALERT HighCPULoad
  IF node_cpu_seconds_total{mode="idle"} < 10
  FOR 5m
  LABELS { severity="critical" }
  ANNOTATIONS {
    summary = "High CPU load on instance {{$labels.instance}}",
    description = "CPU usage is critically high on instance {{$labels.instance}}",
  }
```
This example defines an alert rule that triggers when the idle CPU time is less than 10 for 5 minutes.  