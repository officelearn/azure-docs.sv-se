---
title: Aggregate operations on Azure Cosmos DB Cassandra API tables from Spark (Mängdåtgärder i Azure Cosmos DB med API för Cassandra-tabeller från Spark)
description: Den här artikeln innehåller grundläggande aggregeringsåtgärder mot Azure Cosmos DB Cassandra API-tabeller från Spark
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4fbb86f4fbda9b8e521f7465bb8bb3d18602ca13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60894194"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Aggregate operations on Azure Cosmos DB Cassandra API tables from Spark (Mängdåtgärder i Azure Cosmos DB med API för Cassandra-tabeller från Spark) 

I den här artikeln beskrivs grundläggande aggregeringsåtgärder mot Azure Cosmos DB med API för Cassandra-tabeller från Spark. 

> [!NOTE]
> Serverfiltrering och aggregering på serversidan stöds för närvarande inte i Azure Cosmos DB Cassandra API.

## <a name="cassandra-api-configuration"></a>Cassandra API-konfiguration

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="sample-data-generator"></a>Exempel på datagenerator

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Åtgärden Antal


### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").count
```

**Resultat:**
```
res48: Long = 5
```

### <a name="dataframe-api"></a>Api för dataram

Antal mot dataramar stöds för närvarande inte.  Exemplet nedan visar hur du kör ett dataromantal efter att dataramen har sparats till minnet som en lösning.

Välj ett [lagringsalternativ]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) bland följande tillgängliga alternativ för att undvika att stöta på problem med på minne:

* MEMORY_ONLY: Detta är standardlagringsalternativet. Lagrar RDD som deserialiserade Java-objekt i JVM. Om RDD inte får plats i minnet cachelagras inte vissa partitioner och de beräknas om i farten varje gång de behövs.

* MEMORY_AND_DISK: Lagrar RDD som deserialiserade Java-objekt i JVM. Om RDD:n inte får plats i minnet lagrar du de partitioner som inte får plats på disken och läser dem från den plats de lagras när det behövs.

* MEMORY_ONLY_SER (Java/Scala): Lagrar RDD som serialiserade Java-objekt- en byte-matris per partition. Det här alternativet är utrymmeseffektivt jämfört med deserialiserade objekt, särskilt när du använder en snabb serialiserare, men mer CPU-intensiv att läsa.

* MEMORY_AND_DISK_SER (Java/Scala): Det här lagringsalternativet är som MEMORY_ONLY_SER, är den enda skillnaden att det spiller partitioner som inte får plats i diskminnet istället för att omdatorn dem när de behövs.

* DISK_ONLY: Lagrar endast RDD-partitioner på disken.

* MEMORY_ONLY_2 MEMORY_AND_DISK_2...: Samma som nivåerna ovan, men replikerar varje partition på två klusternoder.

* OFF_HEAP (experimentell): Liknar MEMORY_ONLY_SER, men lagrar data i off-heap minne, och det kräver off-heap minne aktiveras i förväg. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Genomsnittlig åtgärd

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Resultat:**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Api för dataram

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Resultat:**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Resultat:**
```
16.016000175476073
```

## <a name="min-operation"></a>Min drift

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Resultat:**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Api för dataram

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Resultat:**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**Resultat:**
```
11.33
```

## <a name="max-operation"></a>Max drift

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Api för dataram

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Resultat:**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**Resultat:**
```
22.45
```

## <a name="sum-operation"></a>Summaåtgärd

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Resultat:**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Api för dataram

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Resultat:**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Resultat:**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Topp- eller jämförbar drift

### <a name="rdd-api"></a>RDD-API

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Resultat:**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Api för dataram

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Resultat:**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Nästa steg

Så här utför du tabellkopieringsåtgärder:

* [Tabellkopieringsåtgärder](cassandra-spark-table-copy-ops.md)
