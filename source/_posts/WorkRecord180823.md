---
title: 工作中问题记录180322
id: 20180823
tags:
  - docker
  - elk
categories:
  - 其他
  - docker

date: 2018-08-23 03:36:45
---

docker run -it -p 8761:8761 --name qc-eureka qc-eureka-0.0.1.jar -d

docker 安装elk
docker pull sebp/elk
错误：max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
sudo sysctl -w vm.max_map_count=262144
查看日志：
sudo docker logs -ft --tail 100 elk

sudo docker pull wurstmeister/kafka
sudo docker pull wurstmeister/zookeeper
sudo docker run -d --name zookeeper -p 2181:2181 -t wurstmeister/zookeeper

sudo docker run -d --name kafka --publish 9092:9092 --link zookeeper -e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 -e KAFKA_ADVERTISED_HOST_NAME=localhost -e KAFKA_ADVERTISED_PORT=9092 wurstmeister/kafka

sudo docker run -it --name=elk-2 -p 5602:5601 -p 9201:9200-p 5045:5044 -v ~/elk/kibana.yml:/opt/kibana/config/kibana.yml -v ~/elk/index.js:/opt/kibana/src/core_plugins/kibana/index.js -v ~/elk/translations:/opt/kibana/src/core_plugins/kibana/translations -e ES_MIN_MEM=128m  -e ES_MAX_MEM=1024m -d sebp/elk

进入镜像
sudo docker exec -it elk /bin/bash