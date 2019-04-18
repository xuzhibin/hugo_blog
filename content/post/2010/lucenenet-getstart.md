---
title: Lucene.Net入门基础
date: 2010-06-13
categories:
- .net
tags:
- lucene
---
# 简单的例子


``` csharp
//索引
Private void Index()
{
    IndexWriter writer = new IndexWriter(@"E:\Index", new StandardAnalyzer());
    Document doc = new Document();
    doc.Add(new Field("Text","哦耶,美丽的姑娘。", Field.Store.YES, Field.Index.TOKENIZED));
    writer.AddDocument(doc);
    writer.Close();
}

//搜索
Private void Search(string words)
{
    IndexSearcher searcher = new IndexSearcher(@"E:\Index");
    Query query = new QueryParser(“Text”, new StandardAnalyzer()).Parse(words);
    Hits hits = searcher.Search(query);
    for (int i = 0; i < hits.Length(); i++)
        System.Console.WriteLine(hits.Doc(i).GetField("Text").StringValue();
    searcher.Close();
}
```


# 初识Lucene
## Lucene是什么
Lucene是一个高性能的、可扩展的信息检索工具包。它只是Java类库，并不是现成的应用程序。它提供简单易用却十分强大的API接口，基于它你可以快速的构建功能强大的搜索程序（搜索引擎？）。当前最新版2.9.2.1。
![](http://blog-img.xuzhibin.com/2018-11-28-081017.gif)

## 什么是索引
为了实现快速的搜索，Lucene会首先将需要处理的数据以一种称为倒排索引（Inverted Index）的数据结构进行存储。怎样理解倒排索引呢？简单的说，倒排索引并不是回答“这个文档中包含哪些单词？”这个问题，而是经过优化以后用来快速回答“哪些文档包含词XX？”这个问题。就像需要给书籍整理一份供快速查找的目录一样，Lucene也得为需要被搜索的数据整理优化出一份索引文件(Index file)，而这个过程称之为“索引”(Indexing)。

##  Lucene的核心类
索引过程：

IndexWriter Directory Analyzer Document Field

搜索过程：

IndexSearcher Term Query TermQuery Hits

# 索引
## 索引过程的流程图
![](http://blog-img.xuzhibin.com/2018-11-28-081018.gif)
注:Lucene索引过程分为三个主要的操作阶段：将数据换转成文本、分析文本、并将分析过的文本保存到索引库中

## 基本的索引操作
### 添加索引
Document 

Field（理解Field的参数）

异构Document

追加域

增量索引

### 删除索引
软删除，仅添加了删除标记。调用 IndexWriter.Optimize() 后真正删除。

``` csharp
IndexReader reader = IndexReader.Open(directory);

// 删除指定序号(DocId)的 Document。
reader.Delete(123);

// 删除包含指定 Term 的 Document。
reader.Delete(new Term(FieldValue, "Hello"));

// 恢复软删除。
reader.UndeleteAll();

reader.Close();
```

### 更新索引
事实上，Lucene没有更新索引的方法

更新 = 删除 + 添加

提示：当删除和添加多个Document对象时，最好进行批量处理。这样做的速度总是比交替的删除和添加操作的速度快得多。


``` csharp
//只需将 create 参数设为 false，即可往现有索引库添加新数据。
Directory directory = FSDirectory.GetDirectory("index", false);
IndexWriter writer = new IndexWriter(directory, analyzer, false);
writer.AddDocument(doc1);
writer.AddDocument(doc2);
writer.Optimize();
writer.Close();
```

## 加权(boosing)
可以给 Document 和 Field 增加权重(Boost)，使其在搜索结果排名更加靠前。缺省情况下，搜索结果以 Document.Score 作为排序依据，该数值越大排名越靠前。Boost 缺省值为 1。

**Score = Score * Boost**

通过上面的公式，我们就可以设置不同的权重来影响排名。
如下面的例子中根据 VIP 级别设定不同的权重。


``` csharp
Document document = new Document();
switch (vip)
{
  case VIP.Gold: document.SetBoost(2F); break;
  case VIP.Argentine: document.SetBoost(1.5F); break;
}
```

只要 Boost 足够大，那么就可以让某个命中结果永远排第一位，这就是百度等网站的”收费排名”业务。

## Directory
从指定目录打开已有索引库。


``` csharp
private Directory directory = FSDirectory.GetDirectory("c:\index", false);
```

将索引库载入内存，以提高搜索速度。


``` csharp
private Directory directory = new RAMDirectory(FSDirectory.GetDirectory(@"c:\index", false));
//或
//private Directory directory = new RAMDirectory(c:\index");
```

注意 FSDirectory.GetDirectory 的 create 参数，为 true 时将删除已有索引库文件，可以通过 IndexReader.IndexExists() 方法判断。

## 合并索引库
将 directory1 合并到 directory2 中。


``` csharp
Directory directory1 = FSDirectory.GetDirectory("index1", false);
Directory directory2 = FSDirectory.GetDirectory("index2", false);

IndexWriter writer = new IndexWriter(directory2, analyzer, false);
writer.AddIndexes(new Directory[] { directory });
Console.WriteLine(writer.DocCount());
writer.Close();
```

## 优化索引
1. 很简单，一个writer.Optimize()搞定，优化过程会降低索引的效率，优化结果提高搜索性能。不要时时Optimize()，优化一次就够了
2. 
2. 批量向 FSDirectory 增加索引时，增大合并因子(mergeFactor )和最小文档合并数(minMergeDocs)有助于提高性能，减少索引时间。


``` csharp
IndexWriter writer = new IndexWriter(directory, analyzer, true);

writer.maxFieldLength = 1000; // 字段最大长度
writer.mergeFactor = 1000;
writer.minMergeDocs = 1000;

for (int i = 0; i < 10000; i++)
{
  // Add Documentes...
}

writer.Optimize();
writer.Close();
```

利用 Lucene，在创建索引的工程中你可以充分利用机器的硬件资源来提高索引的效率。当你需要索引大量的文件时，你会注意到索引过程的瓶颈是在往磁盘上写索引文件的过程中。为了解决这个问题, Lucene 在内存中持有一块缓冲区。但我们如何控制 Lucene 的缓冲区呢？幸运的是，Lucene 的类 IndexWriter 提供了三个参数用来调整缓冲区的大小以及往磁盘上写索引文件的频率。

（1）合并因子 (mergeFactor)

这个参数决定了在 Lucene 的一个索引块中可以存放多少文档以及把磁盘上的索引块合并成一个大的索引块的频率。比如，如果合并因子的值是 10，那么当内存中的文档数达到 10 的时候所有的文档都必须写到磁盘上的一个新的索引块中。并且，如果磁盘上的索引块的隔数达到 10 的话，这 10 个索引块会被合并成一个新的索引块。这个参数的默认值是 10，如果需要索引的文档数非常多的话这个值将是非常不合适的。对批处理的索引来讲，为这个参数赋一个比较大的值会得到比较好的索引效果。

（2）最小合并文档数 (minMergeDocs)

这个参数也会影响索引的性能。它决定了内存中的文档数至少达到多少才能将它们写回磁盘。这个参数的默认值是10，如果你有足够的内存，那么将这个值尽量设的比较大一些将会显著的提高索引性能。

（3）最大合并文档数 (maxMergeDocs)

这个参数决定了一个索引块中的最大的文档数。它的默认值是 Integer.MAX_VALUE，将这个参数设置为比较大的值可以提高索引效率和检索速度，由于该参数的默认值是整型的最大值，所以我们一般不需要改动这个参数。

## 大数据量索引（并发性、多线程和锁机制）
### 多线程索引
共享对象（注：一个IndexWriter或IndexReader对象可以被多个线程所共享）
巧用RAMDirectory
### 安全锁
Lucene使用基于文件的锁
write.lock
禁用索引锁 (disableLuceneLocks=true)
### 并发访问的规则
任意数量的只读操作都可以同时执行。
在索引正在被修改时，我们也可以同时执行任意数量的只读操作。
在某一时刻，只允许执行一个修改索引的操作。

# 搜索
## IndexSearcher
通过IndexSearcher执行搜索

两种构建IndexSearcher对象的方法： Directory对象与文件路径。 (前者是推荐的)
Search()方法

##  Query
### 创建Query对象
使用QueryParset构建Query对象。（注：QueryParset把查询表达式转换成Lucene内置的查询类型。）

几个常用的内置类型：TermQuery、RangeQuery、PrefixQuery、BooleanQuery。

### 强悍的QueryParser
Query类的toString()方法
布尔查询 （AND、 OR、 NOT） 例：a AND b(+a +b) a OR b(a b) a AND NOT b(+a -b)

组合查询 圆括号”()” 例： (a OR b) AND c

域的选择 例：tag:美女

范围查询 [ TO ] 和{ TO } 例：price:[100 TO 200] price:{100 TO 200}

……

(注：强悍，但不建议使用它)

## Hits
### 使用Hits对象访问搜索结果
### Hits类的几个方法

Length()	Hits对象集合中所包含的文档的数量

Document(n) 排名第n的Document实例

Id(n)	排名第n的DocumentID

Score(n)	排名第n的标准分值


## 排序
### 使用Sort对象排序
通过 SortField 的构造参数，我们可以设置排序字段，排序条件，以及倒排。

```csharp
Sort sort = new Sort(new SortField(FieldName, SortField.DOC, false));
IndexSearcher searcher = new IndexSearcher(reader);
Hits hits = searcher.Search(query, sort);
```

### 按照索引顺序(索引时的文档ID)排序 
使用Sort.INDEXORDER作为参数
### 多域排序
### 排序对性能的影响
排序对搜索速度影响还是很大的，尽可能不要使用多个排序条件。

建议：采用默认的积分排序,设计良好的加权机制

## 过滤
过滤(Filtering)是Lucene中用于缩小搜索空间的一种机制。

DateFliter 只限于指定日期域的值在某一时间范围

QueryFilter 把查询作果作为另一个新查询可搜索的文档空间。

建议：过滤器采取的是对搜索结果的再处理方式，会使程序的性能显著下降，一般推荐使用BooleanQuery组合更多的搜索条件来达成效果。

例子：

我们搜索上架时间在 2005-10-1 到 2005-10-30 之间的商品。

对于日期时间，我们需要转换一下才能添加到索引库，同时还必须是索引字段。


``` csharp
// index
document.Add(FieldDate, DateField.DateToString(date), Field.Store.YES, Field.Index.UN_TOKENIZED);
//...
// search
Filter filter = new DateFilter(FieldDate, DateTime.Parse("2005-10-1"), DateTime.Parse("2005-10-30"));
Hits hits = searcher.Search(query, filter);
```

除了日期时间，还可以使用整数。比如搜索价格在 100 ~ 200 之间的商品。

Lucene.Net NumberTools 对于数字进行了补位处理，如果需要使用浮点数可以自己参考源码进行。


``` csharp
// index
document.Add(new Field(FieldNumber, NumberTools.LongToString((long)price), Field.Store.YES, Field.Index.UN_TOKENIZED));
//...
// search
Filter filter = new RangeFilter(FieldNumber, NumberTools.LongToString(100L), NumberTools.LongToString(200L), true, true);
Hits hits = searcher.Search(query, filter);
使用 Query 作为过滤条件。

QueryFilter filter = new QueryFilter(QueryParser.Parse("name2", FieldValue, analyzer));
```

我们还可以使用 FilteredQuery 进行多条件过滤。


``` csharp
Filter filter = new DateFilter(FieldDate, DateTime.Parse("2005-10-10"), DateTime.Parse("2005-10-15"));
Filter filter2 = new RangeFilter(FieldNumber, NumberTools.LongToString(11L), NumberTools.LongToString(13L), true, true);

Query query = QueryParser.Parse("name*", FieldName, analyzer);
query = new FilteredQuery(query, filter);
query = new FilteredQuery(query, filter2);

IndexSearcher searcher = new IndexSearcher(reader);
Hits hits = searcher.Search(query);
```

## 多域搜索
使用MultiFieldQueryParser实现多域搜索

权重影响域的优先级，而不是域的使用顺序


``` csharp
Query query = MultiFieldQueryParser.Parse("name*", new string[] { FieldName, FieldValue }, analyzer);

IndexReader reader = IndexReader.Open(directory);
IndexSearcher searcher = new IndexSearcher(reader);
Hits hits = searcher.Search(query);
```

## 组合搜索
除了使用 QueryParser.Parse 分解复杂的搜索语法外，还可以通过组合多个 Query 来达到目的。


``` csharp
Query query1 = new TermQuery(new Term(FieldValue, "name1")); //词语搜索
Query query2 = new WildcardQuery(new Term(FieldName, "name*")); //通配符 
Query query3 = new PrefixQuery(new Term(FieldName, "name1")); //字段搜索 Field:Keyword，自动在结尾添加 *
Query query4 = new RangeQuery(new Term(FieldNumber, NumberTools.LongToString(11L)), new Term(FieldNumber, NumberTools.LongToString(13L)), true); //范围搜索
Query query5 = new FilteredQuery(query, filter); //带过滤条件的搜索
      
BooleanQuery query = new BooleanQuery();
query.Add(query1, BooleanClause.Occur.MUST);
query.Add(query2, BooleanClause.Occur.MUST);

IndexSearcher searcher = new IndexSearcher(reader);
Hits hits = searcher.Search(query);
```

## 分布搜索
我们可以使用 MultiReader 或 MultiSearcher 搜索多个索引库。


``` csharp
MultiReader reader = new MultiReader(new IndexReader[] { IndexReader.Open(@"c:\index"), IndexReader.Open(@"\\server\index") });
IndexSearcher searcher = new IndexSearcher(reader);
Hits hits = searcher.Search(query);
```

或


``` csharp
IndexSearcher searcher1 = new IndexSearcher(reader1);
IndexSearcher searcher2 = new IndexSearcher(reader2);
MultiSearcher searcher = new MultiSearcher(new Searchable[] { searcher1, searcher2 });
Hits hits = searcher.Search(query);
```

还可以使用 ParallelMultiSearcher 进行多线程并行搜索。

## 显示搜索语法字符串
我们组合了很多种搜索条件，或许想看看与其对等的搜索语法串是什么样的。


``` csharp
BooleanQuery query = new BooleanQuery();
query.Add(query1, true, false);
query.Add(query2, true, false);
//...
Console.WriteLine("Syntax: {0}", query.ToString());
输出：
Syntax: +(name:name* value:name*) +number:[0000000000000000b TO 0000000000000000d]
```


# 分词
## 何谓分析器
分析(Analysis)，在Lucene当中指的是将域(Field)文本转换为最基本的索引单元——项(Term)的过程。

## 内置的分析器
KeywordAnalyzer
SimpleAnalyzer
StopAnalyzer
WhitespaceAnalyzer
StandardAnalyzer (最强大的了)

## 中文分词
官方没有自带的中文分词，可以选择第三方的开源中文分词，如盘古分词

[例子源码下载 SourceCode](/uploads/lucenenet-getstart/LuceneDemo.rar)

![](http://blog-img.xuzhibin.com/2018-11-28-081019.gif)

PS：例子程序采用的的Lucene.Net版本为2.9.2.1，文中所举例子程序未必能兼容最新版本，用法请以例子程序为准。
例子程序采用的中文分词为盘古分词。其官网为http://pangusegment.codeplex.com/

# 参考资料:
王振春同学的Lucene.Net培训资料
[Lucene.Net] 基本用法 http://www.rainsts.net/article.asp?id=313