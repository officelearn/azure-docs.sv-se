---
title: Sammanställd åtgärder för Azure Cosmos DB Cassandra API tabeller från Spark
description: Den här artikeln beskriver grundläggande aggregeringsåtgärder mot Azure Cosmos DB Cassandra API tabeller från Spark
author: rockboyfor
ms.author: v-yeche
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
origin.date: 09/24/2018
ms.date: 04/15/2019
ms.openlocfilehash: 4fbb86f4fbda9b8e521f7465bb8bb3d18602ca13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894194"
---
<!--Verify sucessfully-->
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Sammanställd åtgärder för Azure Cosmos DB Cassandra API tabeller från Spark 

Den här artikeln beskriver grundläggande aggregeringsåtgärder mot Azure Cosmos DB Cassandra API tabeller från Spark. 

> [!NOTE]
> Serversidan filtrering och sammanställning för serversidan stöds för närvarande inte i Azure Cosmos DB Cassandra-API.

## <a name="cassandra-api-configuration"></a>Cassandra-API-konfiguration

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.cn")
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
## <a name="sample-data-generator"></a>Exemplet datagenerator

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

## <a name="count-operation"></a>Antal åtgärden

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").count
```

**Utdata:**
```
res48: Long = 5
```

### <a name="dataframe-api"></a>Nu när dataramen API

Antal mot dataramar stöds för närvarande inte.  Exemplet nedan visar hur du kör en dataram antal efter spara dataframe till minne som en tillfällig lösning.

Välj en [lagringsalternativ]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) bland följande tillgängliga alternativ att undvika körs i ”utanför minnet” problem:

* MEMORY_ONLY: Det här är standardalternativet för lagring. Butiker RDD som avserialiserade Java-objekt i JVM. Om RDD inte får plats i minnet, vissa partitioner cachelagras inte och de är recomputed direkt varje gång de behövs.

* MEMORY_AND_DISK: Butiker RDD som avserialiserade Java-objekt i JVM. Om RDD inte får plats i minnet, lagra de partitioner som inte får plats på disken och när det behövs kan du läsa dem var de lagras.

* MEMORY_ONLY_SER (Java/Scala): Butiker RDD som serialiserade Java objekt 1 byte-matris per partition. Det här alternativet är utrymme-effektiv jämfört med avserialiserade objekt, särskilt om du använder en snabb serialiserare, men mer CPU-intensiva att läsa.

* MEMORY_AND_DISK_SER (Java/Scala): Det här lagringsalternativet liknar MEMORY_ONLY_SER, den enda skillnaden är att den spill partitioner som inte passar in i minnet disk i stället för recomputing dem när de behövs.

* DISK_ONLY: Lagrar RDD-partitioner på disken endast.

* MEMORY_ONLY_2 MEMORY_AND_DISK_2...: Samma som nivåerna som ovan, replikerar men varje partition på två noder.

* OFF_HEAP (experimentell): Liknar MEMORY_ONLY_SER, men det lagrar data i av heap-minne och det krävs av heap-minne är aktiverat i tid. 

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

## <a name="average-operation"></a>Medelvärdesoperationen

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Utdata:**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Nu när dataramen API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Utdata:**
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
**Utdata:**
```
16.016000175476073
```

## <a name="min-operation"></a>Min-åtgärden

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Utdata:**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Nu när dataramen API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Utdata:**
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

**Utdata:**
```
11.33
```

## <a name="max-operation"></a>Max-åtgärden

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Nu när dataramen API

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Utdata:**
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
**Utdata:**
```
22.45
```

## <a name="sum-operation"></a>Summeringsåtgärden

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Utdata:**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Nu när dataramen API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Utdata:**
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

**Utdata:**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Övre eller jämförbar

### <a name="rdd-api"></a>RDD-API

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Utdata:**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Nu när dataramen API

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

**Utdata:**
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

Om du vill utföra åtgärder för tabeller med kopia, se:

* [Tabellen kopieringsåtgärder](cassandra-spark-table-copy-ops.md)

<!--Verify sucessfully-->
<!--Update_Description: wording update -->
