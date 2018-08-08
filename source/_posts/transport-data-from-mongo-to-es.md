---
title: 将MongoDB数据加载到Elasticsearch
date: 2018-08-03 22:32:52
tags: MongoDB,Docker,Elasticsearch
comments: true
---

### 一、前言
  由于业务需要，需要将放在MongoDB的数据加载到Elasticsearch，Elasticsearch天生具有全文检索优势。MongoDB虽然新的版本也支持fulltext，但目前尚未支持中文。所以这里我们就用比较流行的Elasticsearch。
另外，为了方便，我会基于Docker搭建MongoDB和Elasticsearch集群

### 二、docker的安装和使用
环境：Ubuntu 16.04.5 LTS

#### 2.1 如果存在旧版本docker，先删除旧版本
$ sudo apt-get remove docker docker-engine docker.io

#### 2.2 更新apt-get
$sudo apt-get update

#### 2.3 添加docker官方 GPG key
```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo apt-key fingerprint 0EBFCD88
pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22
```

#### 2.4 安装docker
>$ sudo apt-get install docker-ce

至此，docker已经安装完成，直接输入docker命令可查看docker的帮助，如命令docker version查看docker版本信息

参考：https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce-1

### 三、搭建MongoDB服务
这里我们就直接使用docker仓库的mongodb镜像。

#### 3.1 查询mongodb镜像
jogen@mymachine:~/test/es$ docker search mongo
NAME                                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mongo                               MongoDB document databases provide high avai…   4773                [OK]                
mongo-express                       Web-based MongoDB admin interface, written w…   274                 [OK]                
#### 3.2 docker pull拉取镜像
$docker pull mongo
#### 3.3 从docker镜像安装mongo
 docker run --name mongodb2 -p 27018:27017 -d mongo --replSet mongoReplSet
--replSet：添加复制集
查看复制机状态：rs.status()
查看配置：rs.conf()
初始化：rs.initiate()
修改配置：rs.reconfig(cfg),具体使用可以查看官网文档，我们这里会修改主机名为ip地址。
#### 3.4 使用mongodb-compass
mongodb-compass：是mongodb的可视化工具，访问mongodb比较方便，这里使用到了它的数据导入导出功能，该工具比较方便。
当然RobotT3也比较好用，但是我找不到数据导入导出功能。
#### 3.5 建立database和collection，并导入一些数据。

### 四、搭建Elastisearch集群服务

#### 4.1 查询镜像
> docker search elastisearch

#### 4.2 拉取镜像到本地
>docker pull docker.elastic.co/elasticsearch/elasticsearch:5.6.10

docker官方中央仓库维护的es已经不再维护，elastic已经自己建立了自己的私有库，并维护。

docker官方仓库的elastic镜像地址：https://hub.docker.com/_/elasticsearch/, elastic6版本之后的，都放在elastic自己的docker仓库维护了。
elastic的仓库是：https://www.docker.elastic.co/

#### 4.3 使用安装docker-compose
```
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
$ docker-compose --version
```
参考：https://docs.docker.com/compose/install/#install-compose

#### 4.4 创建docker-compose.yml文件
本地新建一个目录，如elastictest，存放自己的项目项目信息，docker-compose.yml也放在里面，其内容：
```
version: '2'
services:
  elasticsearch1:
    image: docker.elastic.co/elasticsearch/elasticsearch:5.6.10
    container_name: elasticsearch1
    environment:
      - cluster.name=docker-cluster
      - xpack.security.enabled=false
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    mem_limit: 1g
    volumes:
      - esdata1:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - esnet
  elasticsearch2:
    image: docker.elastic.co/elasticsearch/elasticsearch:5.6.10
    environment:
      - cluster.name=docker-cluster
      - xpack.security.enabled=false
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - "discovery.zen.ping.unicast.hosts=elasticsearch1"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    mem_limit: 1g
    volumes:
      - esdata2:/usr/share/elasticsearch/data
    networks:
      - esnet

volumes:
  esdata1:
    driver: local
  esdata2:
    driver: local

networks:
  esnet:
```

说明： - xpack.security.enabled=false 配置这个，是因为docker中的elasticsearch默认安装了x-path插件，该插件在我们访问es时需要输入账号密码（默认是elastic/changeme），在数据同步的时候可能会比较麻烦.

#### 4.5 启动服务
docker-compose up
#### 4.6 安装elasticsearch-head扩展插件
ElasticSearch-Head插件有助于查看和管理es集群，有多种安装方式，可以参考：https://github.com/mobz/elasticsearch-head
我这里是使用chrome扩展方式，比较方便。
进入chrome的Web-store，搜索ElasticSearch Head并安装

#### 4.7 访问集群
es集群启动好之后，通过head插件访问结果如下：


### 五、使用Transporter传输数据
现在MongoDB和ES集群都已经启动起来了，接下来就是数据同步的问题，从MongoDB到ElasticSearch的同步有多种方式：
    
    1. mongo-connector：
    2. logstach
    3. Transporter
    4. elasticsearch-river-mongodb
    5. Mongoosastic
具体的区别可参考：https://code.likeagirl.io/5-different-ways-to-synchronize-data-from-mongodb-to-elasticsearch-d8456b83d44f
一开始，我使用的是mongo-connector方式，但只支持es5.x版本，到目前位置，尚未支持es6.x。一个原因可能是es更新太快，mongo-connector已经力不从心。我们从github上看到，已经半年没有更新了，一个致命的点就是跟es的版本强相关。所以我上面docker镜像也是使用的5.x版本的原因。其实目前es已经到6.3.2版本了。
此外，在使用mongo-connector过程中，需要用到elastic2_doc_manager中间组件，运行时经常同步不了数据，一运行同步程序，就卡主，没有任何日志输出。这让人难以接受。于是就想尝试使用tansporter。

transporter的使用参考：https://github.com/compose/transporter
这里列出主要步骤：
    
1. 下载transporter客户端
https://github.com/compose/transporter/releases/download/v0.5.2/transporter-0.5.2-linux-amd64
添加执行权限：chmod +x transporter-0.5.2-linux-amd64
修改名称：mv transporter-0.5.2-linux-amd64 transporter
发送到、/usr/bin 目录，以供全局使用：mv transporter /usr/bin

2. transporter初始化
在项目目录elastictest执行：transporter init mongodb elasticsearch
执行完后，在本地生成一个pipline.js文件，文件内容：
```
var source = mongodb({
  "uri": "mongodb://127.0.0.1:27017/enterprise"
  // "timeout": "30s",
  // "tail": false,
  // "ssl": false,
  // "cacerts": ["/path/to/cert.pem"],
  // "wc": 1,
  // "fsync": false,
  // "bulk": false,
  // "collection_filters": "{}",
  // "read_preference": "Primary"
})

var sink = elasticsearch({
  "uri": "http://localhost:9200/enterprise"
  // "timeout": "10s", // defaults to 30s
  // "aws_access_key": "ABCDEF", // used for signing requests to AWS Elasticsearch service
  // "aws_access_secret": "ABCDEF" // used for signing requests to AWS Elasticsearch service
  // "parent_id": "elastic_parent" // defaults to "elastic_parent" parent identifier for Elasticsearch
})

t.Source("source", source, "/.*/").Save("sink", sink, "/.*/")
```

住需要修改两个地方的uri，这里我已经修改了。

3. 执行数据同步
transporter run
到此数据同步完成，可以开心地打开head扩展插件查看同步结果了。

可以参考：https://aboullaite.me/sync-data-from-mongodb-to-elasticsearch-using-transporter/

### 六、总结
在实践过程中还是需要多尝试，使用mongo-connector的过程中，甚至出现需要去直接修改源码的情况，比如请求过程中的headers缺少了Content-Type，需要手动添加进去。




