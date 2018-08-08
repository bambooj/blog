---
title: Elasticsearch中文全文检索
date: 2018-08-08 22:59:41
tags:
---


前提：安装好ik-analyze
### 1. 创建index

```
curl -XPUT http://localhost:9200/poetry
```

查询索引：
```
curl -XGET http://localhost:9200/indices
```
查询结果：
```
hexin@hexin:~/test/es$ curl localhost:9200/_cat/indices?pretty
green open poetry                        XxSNsdRLSxifRwg22Ezn6Q 5 1     0   0    810b   324b
```

### 2. 创建type的mapping。

查看index（或者map下面的type）的mapping
```

curl -XPOST http://localhost:9200/poetry/fulltext/_mapping -H 'Content-Type:application/json' -d'
{
        "properties": {
            "content": {
                "type": "text",
                "analyzer": "ik_max_word",
                "search_analyzer": "ik_max_word"
            }
        }

}'
```

查看映射
```
curl localhost:9200/poetry/_mapping?pretty
```

查询结果

```
{
  "poetry" : {
    "mappings" : {
      "fulltext" : {
        "properties" : {
          "content" : {
            "type" : "text",
            "analyzer" : "ik_max_word"
          }
        }
      }
    }
  }
}
```

### 3. 插入数据

```
curl -XPOST http://localhost:9200/poetry/fulltext/1 -H 'Content-Type:application/json' -d'
{"content":"轻轻的我走了，正如我轻轻的来"}
'

curl -XPOST http://localhost:9200/poetry/fulltext/2 -H 'Content-Type:application/json' -d'
{"content":"我轻轻的招手，作别西天的云彩"}
'

curl -XPOST http://localhost:9200/poetry/fulltext/3 -H 'Content-Type:application/json' -d'
{"content":"那河畔的金柳，是夕阳中的新娘"}
'

curl -XPOST http://localhost:9200/poetry/fulltext/4 -H 'Content-Type:application/json' -d'
{"content":"波光里的艳影，在我的心头荡漾"}
'

curl -XPOST http://localhost:9200/poetry/fulltext/5 -H 'Content-Type:application/json' -d'
{"content":"在康河的柔波里，我甘心做一条水草"}
'
```

### 4. 测试分词

```
curl -X GET "localhost:9200/poetry/_analyze?pretty" -H 'Content-Type: application/json' -d'
{
  "field": "content",
  "text": "曾经沧海难为水，除却巫山不是云" 
}
'
```

结果：
```
{
  "tokens" : [
    {
      "token" : "曾经沧海难为水",
      "start_offset" : 0,
      "end_offset" : 7,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "曾经沧海",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "CN_WORD",
      "position" : 1
    },
    {
      "token" : "曾经",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "CN_WORD",
      "position" : 2
    },
    {
      "token" : "沧海",
      "start_offset" : 2,
      "end_offset" : 4,
      "type" : "CN_WORD",
      "position" : 3
    },
    {
      "token" : "海难",
      "start_offset" : 3,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 4
    },
    {
      "token" : "难为",
      "start_offset" : 4,
      "end_offset" : 6,
      "type" : "CN_WORD",
      "position" : 5
    },
    {
      "token" : "水",
      "start_offset" : 6,
      "end_offset" : 7,
      "type" : "CN_CHAR",
      "position" : 6
    },
    {
      "token" : "除却巫山不是云",
      "start_offset" : 8,
      "end_offset" : 15,
      "type" : "CN_WORD",
      "position" : 7
    },
    {
      "token" : "除却",
      "start_offset" : 8,
      "end_offset" : 10,
      "type" : "CN_WORD",
      "position" : 8
    },
    {
      "token" : "巫山",
      "start_offset" : 10,
      "end_offset" : 12,
      "type" : "CN_WORD",
      "position" : 9
    },
    {
      "token" : "不是",
      "start_offset" : 12,
      "end_offset" : 14,
      "type" : "CN_WORD",
      "position" : 10
    },
    {
      "token" : "云",
      "start_offset" : 14,
      "end_offset" : 15,
      "type" : "CN_CHAR",
      "position" : 11
    }
  ]
}
```

### 5. 检索数据
   
   4.1 简单检索
	
	curl localhost:9200/poetry/_search?pretty
   
   4.2 全文检索
   
	```
	curl -XPOST http://localhost:9200/poetry/fulltext/_search?pretty  -H 'Content-Type:application/json' -d'
	{
	    "query" : { "match" : { "content" : "轻轻" }},
	    "highlight" : {
		"pre_tags" : ["<tag1>", "<tag2>"],
		"post_tags" : ["</tag1>", "</tag2>"],
		"fields" : {
		    "content" : {}
		}
	    }
	}
	'
	```

   4.2 聚合查询