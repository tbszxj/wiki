# elk学习

ELK是Elasticsearch、Logstash、Kibana三大开源框架首字母大写简称。市面上也被成为Elastic Stack。

## 一、Elasticsearch相关

### 1.1 Docker 安装 Elasticsearch

```shell
# 下载镜像
sudo docker pull elasticsearch:7.6.0
# 创建用户定义的网络（用于连接到同一网络的其他服务，例如Kibana）
sudo docker network create somenetwork
# 运行 elasticsearch
sudo docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.0
# 查看容器状态
sudo docker ps
#进入容器内部
sudo docker exec -it elasticsearch /bin/bash
#修改elasticsearch.yml配置文件(内容见下面)

#重启容器
sudo docker restart elasticsearch
# 检测 elasticsearch 是否启动成功
curl 127.0.0.1:9200
```

```yml
#elasticsearch.yml
cluster.name: "docker-cluster"
network.host: 0.0.0.0
# 允许任何端口访问
transport.host: 0.0.0.0
```

```json
#启动成功curl响应结果
{
  "name" : "e9c750188b11",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "mw3Zj-0IT1K2TF-hewVA1g",
  "version" : {
    "number" : "7.6.0",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "7f634e9f44834fbc12724506cc1da681b0c3b1e3",
    "build_date" : "2020-02-06T00:09:00.449973Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

## 二、Kibana相关

### 2.1 Docker安装Kibana

```shell
# 下载镜像
sudo docker pull kibana:7.6.0
# 运行 kibana
sudo docker run -d --name kibana --net somenetwork -p 5601:5601 kibana:7.6.0
# 查看容器状态
sudo docker ps
#进入容器内部
sudo docker exec -it kibana /bin/bash
#修改kibana.yml配置文件（见下面）
#重启kibana
sudo docker restart kibana
# 访问检测是否启动成功
http://127.0.0.1:5601
```

```yml
#kibana.yml配置文件内容
server.name: kibana
# 允许所有地址访问
server.host: "0.0.0.0"
# elasticsearch的地址
elasticsearch.url: http://172.19.0.1:9200
xpack.monitoring.ui.container.elasticsearch.enabled: true
```

## 三、Logstash相关

### 3.1 Docker安装Logstash

```shell
# 下载镜像
sudo docker pull logstash:7.6.0
# 运行 Logstash
sudo docker run --name logstash logstash:7.6.0
#进入容器内部

#修改logstash.yml配置文件（见下面）

# 查看容器状态
sudo docker ps
```

### 3.2 修改logstash.yml配置文件

```shell
# 进入容器
sudo docker exec -it logstash /bin/bash
# 进入目录
cd config
# 修改文件
vi logstash.yml
#如果内存不够需要修改jvm.options虚拟机内存大小
```

```yml
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.url: http://172.19.0.1:9200
#xpack.monitoring.elasticsearch.username: elastic
#xpack.monitoring.elasticsearch.password: changme
```

### 3.3 修改pipeline下的logstash.conf文件

```shell
# 修改logstash
docker exec -it logstash /bin/bash

cd pipeline

logstash.conf
vi logstash.conf
```

```
#原来的
#========================================
#input {
#  beats {
#    port => 5044
#  }
#}

#output {
#  stdout {
#    codec => rubydebug
#  }
#}
#========================================
#添加的部分
input {
    beats {
    port => 5044
    codec => "json"
}
}

output {
  elasticsearch { hosts => ["172.19.0.1:9200"] }
  stdout { codec => rubydebug }
}
```

## 四、Filebeat相关

### 4.1 Docker安装Filebeat

```shell
# 下载镜像
sudo docker pull elastic/filebeat:7.6.0
 
# 运行Filebeat
docker run -d --name filebeat --net somenetwork -v /usr/local/filebeat/logs/:/usr/local/filebeat/logs/ -v /usr/share/filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml elastic/filebeat:7.6.0
```



```yml
#/usr/share/filebeat/filebeat.yml
filebeat.inputs:
- type: log
  enabled: true
  paths:
  - /usr/local/filebeat/logs/*.log

output.logstash:
  hosts: ['172.19.0.1:5044']
```

