##安装EPEL
```
sudo yum -y install epel-release
```
##安装Redis
```
sudo yum -y install redis
```
设置开机自启动然后启动：
```
sudo systemctl daemon-reload
sudo systemctl enable redis.service
sudo systemctl start redis.service
```
##安装ELK
只安装ELK：Elasticsearch、Logstash、Kibana，Elastic Stack的其他组件没装。
```
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```
新增文件/etc/yum.repos.d/elasticsearch.repo：
```
[elasticsearch-5.x]
name=Elasticsearch repository for 5.x packages
baseurl=https://mirrors.tuna.tsinghua.edu.cn/elasticstack/5.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```
用的是清华的源。
安装：
```
sudo yum -y install elasticsearch kibana logstash
```
Systemd开启开机自启动：
```
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch.service
sudo systemctl enable kibana.service
```
启动：
```
sudo systemctl start elasticsearch.service
sudo systemctl start kibana.service
sudo systemctl start logstash.service
```
验证一下Elasticsearch：
```
curl http://localhost:9200
{
  "name" : "Nv3NQKr",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "Rx24DAWoS_ySLqeDCPNm0g",
  "version" : {
    "number" : "5.1.1",
    "build_hash" : "5395e21",
    "build_date" : "2016-12-06T12:36:15.409Z",
    "build_snapshot" : false,
    "lucene_version" : "6.3.0"
  },
  "tagline" : "You Know, for Search"
}
```
Elasticsearch和Kibana基本是开箱即用，默认配置就能跑起来。

