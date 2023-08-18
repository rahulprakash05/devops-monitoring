### Install and Configure Prometheus, Grafana and Node-exporter in AWS
```
Node1 : Prometheus
Node2: Grafana & Node-exporter
```
### Node1 : Prometheus
### Installing Prometheus is very simple and straight:
```
wget https://s3-eu-west-1.amazonaws.com/deb.robustperception.io/41EFC99D.gpg | sudo apt-key add -
sudo apt-get update -y
sudo apt-get install prometheus prometheus-node-exporter prometheus-pushgateway prometheus-alertmanager -y
```
### Start Prometheus:
```
sudo systemctl start prometheus
sudo systemctl enable prometheus
```
### Access Prometheus: You can use the public DNS of the EC2 instance to access Prometheus on port 9090, make sure that Port 9090 is enabled in security groups.  
```
URL: http://Prometheus-Instance-IP:9090/
```
### Node2 : Grafana
### Installing Grafana is very simple and straight:
```
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo apt-get update
sudo apt-get install grafana
sudo systemctl daemon-reload
```
### Start Grafana:
```
sudo systemctl start grafana-server
sudo systemctl enable grafana-server.service
```
### Access Grafana: You can access it on port 3000. Enable the port 3000 in the security group if you are using an EC2 instance.
```
URL: http://Grafana-IP:3000/
Username: admin
Password: admin
```
### Node2 : Node-Exporter
### Installing Node-Exporter is very simple and straight:
### You can download the Node_Exporter version of your choice from the following link or use the following commands to download node-exporter-1.0.0. Once you download the tar file, you just need to extract it and copy the binary to  /usr/local/bin/. Link to checkout different versions of node_exporter: https://prometheus.io/download/#node_exporter
```
cd /tmp/
wget https://github.com/prometheus/node_exporter/releases/download/v1.0.0-rc.0/node_exporter-1.0.0-rc.0.linux-amd64.tar.gz
tar -zxvf node_exporter-1.0.0-rc.0.linux-amd64.tar.gz 
cp node_exporter-1.0.0-rc.0.linux-amd64/node_exporter /usr/local/bin/
```
### Configure Node-Exporter:
```
vi /etc/systemd/system/node_exporter.service

[Unit]
Description=Prometheus Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```
### Reload Configurations of Node-Exporter:
```
systemctl daemon-reload
systemctl start node_exporter.service
```
### Access Node-Exporter: Node_Exporter starts on port 9100, Allow this port in the security group.
```
URL: http://IP-of-Node-Exporter:9100/
```

### Integrate Grafana with Prometheus update the below config in Prometheus node
```
vi /etc/prometheus/prometheus.yml
The IP of the Node_Exporter has been added under 'job_name:grafana_node'.
```
### Restart Prometheus:
```
sudo systemctl start prometheus
```
### Access Prometheus: Access the Prometheus on your browser and under execute query history execute type the query "up" and execute it.
```
URL: http://Prometheus-IP:9090/
```
### Configure Grafana, add Prometheus in it.
```
URL: http://Grafana-IP:3000/
```
### On the welcome screen now you can add Prometheus to Grafana under "Add data source". Click on "Add data source".
```
Examples:
node_cpu_seconds_total{cpu="0",mode="nice"}

Install the stress to spike up the CPU
sudo apt-get update
sudo apt-get install stress

stress --cpu 4 --timeout 300
```
``
