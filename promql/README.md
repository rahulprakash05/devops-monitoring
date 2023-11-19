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

8. Retrieve Disk Space Usage Percentage for a Specific Mount Point:
```
100 - ((node_filesystem_free_bytes{mountpoint="/your/mount/point"} / node_filesystem_size_bytes{mountpoint="/your/mount/point"}) * 100)
```
Replace "/your/mount/point" with the actual mount point you are interested in.

### Examples and concepts for querying metrics in K8s & Container Prometheus:

1. Check if Any Pods Are Restarting Frequently:
```
rate(kube_pod_container_status_restarts_total{job="kube-state-metrics"}[5m]) > 0
```
This query checks if there's a non-zero restart rate for any containers in the last 5 minutes.  

2. List Pods Using High CPU:
```
topk(5, sum(rate(container_cpu_usage_seconds_total{container!="POD", pod=~"your-pod-regex", namespace="your-namespace"}[5m])) by (pod, namespace))
```
Replace "your-pod-regex" and "your-namespace" with the appropriate values.  

3. Alert for High Memory Usage in a Namespace:
```
ALERT HighMemoryUsage
  IF (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100 < 10
  FOR 5m
  LABELS { severity="critical" }
  ANNOTATIONS {
    summary = "High Memory Usage in namespace {{ $labels.namespace }}",
    description = "Memory usage is critically high in namespace {{ $labels.namespace }}",
  }
```
This example sets an alert if the available memory in a namespace drops below 10% for 5 minutes.

4. Check for High Network Traffic on a Specific Interface:
```
rate(node_network_receive_bytes_total{device="your-network-interface"}[5m]) + rate(node_network_transmit_bytes_total{device="your-network-interface"}[5m])
```
Replace "your-network-interface" with the actual network interface name.

5. List the Top CPU Consumers in a Namespace:
```
topk(5, sum(rate(container_cpu_usage_seconds_total{container!="POD", namespace="your-namespace"}[5m])) by (pod, container, namespace))
```
Replace "your-namespace" with the appropriate namespace.  

6. Alert for High Pod Restart Rate:
```
ALERT HighPodRestarts
  IF (rate(kube_pod_container_status_restarts_total{job="kube-state-metrics"}[5m]) > 0)
  FOR 5m
  LABELS { severity="warning" }
  ANNOTATIONS {
    summary = "Pods Restarting Frequently",
    description = "Pods are restarting frequently in the cluster",
  }
```
This example sets an alert if any pods are restarting frequently in the last 5 minutes.

7. List all Containers in a Namespace:
```
count(container_memory_usage_bytes) by (container, namespace)
```
This query counts the number of containers in each namespace based on memory usage. You can modify it based on other metrics or labels.

8. Retrieve CPU Usage Percentage by Container:
```
100 - (avg(rate(container_cpu_usage_seconds_total[1m])) by (container) * 100)
```
This query calculates the CPU usage percentage for each container.

9. List Pods with High Memory Usage:
```
topk(5, sum(container_memory_usage_bytes) by (pod))
```
This query lists the top 5 pods with the highest memory usage.

10. List Nodes with High CPU Usage:
```
topk(5, node_load1)
```
This query lists the top 5 nodes with the highest 1-minute load average.

11. List the Most Resource-Intensive Pods:
```
topk(5, sum(container_resource_requests_cpu_cores) by (pod, namespace))
```
This query lists the top 5 pods based on their CPU resource requests.

12. Check for Pending Pods in a Namespace:
```
kube_pod_status_phase{job="kube-state-metrics", phase="Pending", namespace="your-namespace"} > 0
```
Replace "your-namespace" with the specific namespace you're interested in. This query checks if there are any pending pods in the specified namespace.
