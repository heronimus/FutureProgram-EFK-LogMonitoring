# Log Monitoring Lab Demo

![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)

## Installation

### 1. General Requirement On Both VM

- Change your hostname !
```sh
$ hostnamectl set-hostname elastic-master-node-1.future.lokal
```
```sh
$ hostnamectl set-hostname elastic-master-node-2.future.lokal
```

- Use SSH with putty / other terminal
```sh
$ ssh root@192.168.0.XX
```
- Install Additional Tools

```sh
$ yum install install wget net-tools telnet
```

 - Install Java openjdk 11

```sh
$ yum install install java-11-openjdk
```

 - Add New User

```sh
$ useradd futureprogram
```
.

### 2. Install Elasticsearch on Both VM

- Create Directory (Application, Log, Data, PID)
```
$ mkdir /opt/elasticsearch
$ mkdir /var/log/elasticsearch
$ mkdir /var/run/elasticsearch
$ mkdir -p /data/elasticsearch
```
- Download elasticsearch to /opt

```sh
$ wget http://patrickstar.local:7777/elasticsearch/elasticsearch-7.5.2-linux-x86_64.tar.gz -P /opt/
```

- Extract elasticsearch package

```sh
$ tar -xzf /opt/elasticsearch-7.5.2-linux-x86_64.tar.gz -C /opt/elasticsearch --strip 1
```

- Change Ownership (Application, Log, Data, PID)
```sh
$ chown -R futureprogram:futureprogram /var/log/elasticsearch
$ chown -R futureprogram:futureprogram /data/elasticsearch
$ chown -R futureprogram:futureprogram /var/run/elasticsearch
$ chown -R futureprogram:futureprogram /opt/elasticsearch
```
- edit /opt/elasticsearch/config/elasticsearch.yml
```sh
$ wget http://patrickstar.local:7777/elasticsearch/elasticsearch.yml -O /opt/elasticsearch/config/elasticsearch.yml
```

- edit /opt/elasticsearch/config/jvm.options
```sh
# Change your JVM Memory setting based on your VM requirements
```

- edit /etc/sysctl.conf
```sh
## Add this line inside sysctl.conf
vm.max_map_count=262144
```

- reload /etc/sysctl.conf
```sh
$ sysctl --system
```

- Config firewalld management
```sh
$ firewall-cmd --zone=public --permanent --add-port=9200/tcp
$ firewall-cmd --zone=public --permanent --add-port=9300/tcp
$ firewall-cmd --reload
```

- Add systemd service on /etc/systemd/system
```sh
$ wget http://patrickstar.local:7777/elasticsearch/elasticsearch.service -O /etc/systemd/system/elasticsearch.service
$ systemctl daemon-reload
```
- Start Elasticsearch
```sh
$ systemctl start elasticsearch
```

- Check your elasticsearch
```sh
$ #check on your browser !
http://your-vm-ip:9200/_cluster/state?pretty
```

### 2. Install Kibana on VM 1 Only

- Create Directory (Application, Log, Data, PID)
```
$ mkdir /opt/kibana
$ mkdir /var/log/kibana
```

- Download kibana to /opt
```sh
$ wget http://patrickstar.local:7777/kibana/kibana-7.5.2-linux-x86_64.tar.gz -P /opt/
```

- Extract Kibana package
```sh
$ tar -xzf /opt/kibana-7.5.2-linux-x86_64.tar.gz -C /opt/kibana --strip 1
```

- edit kibana configuration /opt/kibana/kibana.yml
```sh
$ wget http://patrickstar.local:7777/kibana/kibana.yml -O /opt/kibana/config/kibana.yml
```

- Change Ownership
```sh
$ chown -R futureprogram:futureprogram /var/log/kibana
$ chown -R futureprogram:futureprogram /opt/kibana
```

- Config firewalld management
```sh
$ firewall-cmd --zone=public --permanent --add-port=5601/tcp
$ firewall-cmd --reload
```

- Add systemd service on /etc/systemd/system
```sh
$ wget http://patrickstar.local:7777/kibana/kibana.service -O /etc/systemd/system/kibana.service
$ systemctl daemon-reload
```

- Start Kibana
```sh
$ systemctl start kibana
```

- Check your Kibana
```sh
$ #check on your browser !
http://your-vm1-IP:5601/
```

### 3. Install Filebeat on VM 2 Only

- Create Directory
```
$ mkdir -p /opt/filebeat/inputs.d
```

- Download filebeat to /opt
```sh
$ wget http://patrickstar.local:7777/filebeat/filebeat-7.5.2-linux-x86_64.tar.gz -P /opt/
```

- Extract filebeat package
```sh
$ tar -xzf /opt/filebeat-7.5.2-linux-x86_64.tar.gz -C /opt/filebeat --strip 1
```

- Set Filebeat Configuration
```sh
wget http://patrickstar.local:7777/filebeat/policy-file.json -P /opt/filebeat/
wget http://patrickstar.local:7777/filebeat/index-template.json -P /opt/filebeat/
wget http://patrickstar.local:7777/filebeat/filebeat.yml -O /opt/filebeat/filebeat.yml
```

- Change Ownership
```sh
$ chown -R futureprogram:futureprogram /opt/filebeat
```

- Add systemd service on /etc/systemd/system
```sh
$ wget http://patrickstar.local:7777/filebeat/filebeat.service -O /etc/systemd/system/filebeat.service
$ systemctl daemon-reload
```
.
#### Lets test our log App !
- Download & run simple app
```sh
$ mkdir /opt/simple-app
$ wget http://patrickstar.local:7777/simple-app/simple-app-1.sh -P /opt/simple-app/
$ chmod +x /opt/simple-app/simple-app-1.sh
$ nohup ./opt/simple-app/simple-app-1.sh &
```

- Check app log on /opt/simple-app/log-app-01.log

- Add App configuration test
```sh
$ wget http://patrickstar.local:7777/filebeat/test-01.yml -P /opt/filebeat/inputs.d/
```

- Start filebeat
```sh
$ systemctl start filebeat
```

### >> LAST Step --> Check your kibana dashboard, and find your log from simple-app-1

.
.
.



**Free Software, Hell Yeah!**
