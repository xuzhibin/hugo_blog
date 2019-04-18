---
title: ElasticSearch入门笔记
date: 2012-09-06
categories: 
- server
tags: 
- elasticsearch
---

ElasticSearch 是构建在Apache Lucene之上的的搜索引擎服务，开源（Apache2协议），分布式，RESTful。安装方便，使用简单。

官方站点：http://www.elasticsearch.com/

中文站点：http://es-cn.medcl.net/

# 安装

必须先安装Java环境，并设置 JAVA_HOME => C:\Program Files\Java\jdk1.6.0_18

**elasticsearch-rtf 中文入门集成包** https://github.com/medcl/elasticsearch-rtf
使用git签出，下载到本地。windows下，执行bin下面的elasticsearch.bat。linux下，执行bin下面或者service下面elasticsearch。

**Pyes** https://github.com/aparo/pyes [更多客户端](https://www.elastic.co/guide/en/elasticsearch/client/index.html)

**Bottle** http://bottlepy.org/docs/dev/

# 角色关系对照

elasticsearch 跟 MySQL 中定义资料格式的角色关系对照表如下

MySQL  | helasticsearch
---|---
database | index
table | type
row | document
field | field



# 索引映射


``` bash
#创建索引
$ curl -XPUT http://localhost:9200/test-index

#创建Mapping
$ curl -XPUT http://localhost:9200/test-index/test-type/_mapping -d '{
    "properties" : {
        "name" : { "type" : "string" }
    }
}'
```

``` python
@route('/indexsetting/')
def indexmapping():
    """索引映射"""
    conn = ES('127.0.0.1:9200')
    conn.debug_dump = True
    try:
        #删除索引
        conn.delete_index("test-index")
    except:
        pass
    #创建索引
    conn.create_index("test-index")
    mapping = {
           u'id': {'store': 'yes',
                    'type': u'integer'},
           u'author': {'boost': 1.0,
                       'index': 'not_analyzed',
                       'store': 'yes',
                       'type': u'string'},
           u'published': {'boost': 1.0,
                          'index': 'not_analyzed',
                          'store': 'yes',
                          'type': u'datetime'},
           u'url': {'store': 'yes',
                    'type': u'string'},
           u'title': {'boost': 1.0,
                       'index': 'analyzed',
                       'store': 'yes',
                       'type': u'string'},
           u'content': {'boost': 1.0,
                       'index': 'analyzed',
                       'store': 'yes',
                       'type': u'string',
                       "term_vector" : "with_positions_offsets"}
           }
    #索引映射
    conn.put_mapping("test-type", {'properties':mapping}, ["test-index"])
    return "索引映射"
```


# 索引


``` bash
#索引
$ curl -XPUT http://localhost:9200/test-index/test-type/1 -d '{
    "user": "kimchy",
    "post_date": "2009-11-15T13:12:00",
    "message": "Trying out elasticsearch, so far so good?"
}'

#获取
$ curl -XGET http://localhost:9200/test-index/test-type/1

#删除
$ curl -XDELETE 'http://localhost:9200/test-index/test-type/1'
```

``` python
@route('/indextest/')
def indexTest():
    """索引测试"""
    conn = ES('127.0.0.1:9200')
    for item in Data().getData():
        #添加索引
        conn.index(item,"test-index", "test-type",item['id'])

    #索引优化
    conn.optimize(["test-index"])
    #删除索引内容
    conn.delete("test-index", "test-type", 2668090)
    #更新索引内容
    model = conn.get("test-index", "test-type", 2667371)
    model["title"]="标题修改测试"
    conn.update(model,"test-index", "test-type",2667371)

    #刷新索引
    conn.refresh(["test-index"])

    q = MatchAllQuery()
    results = conn.search(query = q,indices="test-index", doc_types="test-type")
#    for r in results:
#        print r
    return template('default.tpl', list=results,count=len(results))
```

# 搜索


``` bash
#lucene语法方式的查询
$ curl -XGET http://localhost:9200/test-index/test-type/_search?q=user:kimchy

#query DSL方式查询
$ curl -XGET http://localhost:9200/test-index/test-type/_search -d '{
    "query" : {
        "term" : { "user": "kimchy" }
    }
}'

#query DSL方式查询
$ curl -XGET http://localhost:9200/test-index/_search?pretty=true -d '{
    "query" : {
        "range" : {
            "post_date" : {
                "from" : "2009-11-15T13:00:00",
                "to" : "2009-11-15T14:30:00"
            }
        }
    }
}'

#查找全部索引内容
$ curl -XGET http://localhost:9200/test-index/test-type/_search?pretty=true

```



``` python
@route('/search/')
@route('/search/<searchkey>')
def search(searchkey=u"关键算法"):
    """索引搜索"""
    conn = ES('127.0.0.1:9200')

    #TextQuery会对searchkey进行分词
    qtitle = TextQuery("title", searchkey)
    qcontent = TextQuery("content", searchkey)
    #发布时间大于"2012-9-2 22:00:00"
    qpublished=RangeQuery(ESRangeOp("published", "gt", datetime(2012, 9, 2, 22, 0, 0)))

    h = HighLighter(['<b>'], ['</b>'], fragment_size=500)
    #多字段搜索(must=>and,should=>or)，高亮，结果截取（分页），排序
    q = Search(BoolQuery(must=[qpublished],should=[qtitle,qcontent]), highlight=h, start=0, size=3, sort={'id': {'order': 'asc'}})
    q.add_highlight("title")
    q.add_highlight("content")
    results = conn.search(query = q,indices="test-index", doc_types="test-type")

    list=[]
    for r in results:
        if(r._meta.highlight.has_key("title")):
            r['title']=r._meta.highlight[u"title"][0]
        if(r._meta.highlight.has_key("content")):
            r['content']=r._meta.highlight[u"content"][0]
        list.append(r)
    return template('search.tpl', list=list,count=results.total)
```

# 设置


``` bash
#创建索引，并设置分片和副本参数
$ curl -XPUT http://localhost:9200/elasticsearch/ -d '{
    "settings" : {
        "number_of_shards" : 2,
        "number_of_replicas" : 3
    }
}'
```


# 其他


``` bash
#分词
curl -XGET 'http://localhost:9200/test-index/_analyze?text=中华人民共和国'
```
[例子程序下载](/uploads/files/elasticsearch_demo.rar)