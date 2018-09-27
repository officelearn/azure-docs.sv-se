---
title: Skapa/infoga data i Azure Cosmos DB Cassandra-API från Spark
description: Den här artikeln beskriver hur du infogar exempeldata i Azure Cosmos DB Cassandra API-tabeller
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: 74578dc7e69a1454e815679cf403839c3b7df4d7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220411"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Skapa/infoga data i Azure Cosmos DB Cassandra-API från Spark
 
Den här artikeln beskriver hur du infoga exempeldata i en tabell i Azure Cosmos DB Cassandra-API från Spark.

## <a name="cassandra-api-configuration"></a>Cassandra-API-konfiguration

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
## <a name="dataframe-api"></a>Nu när dataramen API

### <a name="create-a-dataframe-with-sample-data"></a>Skapa en Dataframe med exempeldata

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> ”Skapa if not exists” funktioner på en radnivå, stöds inte ännu.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Spara i ett Azure Cosmos DB Cassandra-API

När du sparar data kan också ange time to live och konsekvens principinställningar som visas i följande exempel:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> På kolumnnivå TTL stöds inte ännu.

#### <a name="validate-in-cqlsh"></a>Verifiera i cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>Flexibel distribuerad databas (RDD) API

### <a name="create-a-rdd-with-sample-data"></a>Skapa en RDD med exempeldata
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> Skapa om det inte finns funktioner stöds inte ännu.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Spara i ett Azure Cosmos DB Cassandra-API

När du sparar data till Cassandra-API kan du också ange time to live och konsekvens principinställningar som visas i följande exempel:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Verifiera i cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Nästa steg

När du har infogat data i Azure Cosmos DB Cassandra API-tabellen, fortsätter du till följande artiklar för att utföra andra åtgärder i data som lagras i Cosmos DB Cassandra-API:
 
* [läsåtgärder](cassandra-spark-read-ops.md)
* [Upsert åtgärder](cassandra-spark-upsert-ops.md)
* [Borttagningsåtgärder](cassandra-spark-delete-ops.md)
* [Aggregeringsåtgärder](cassandra-spark-aggregation-ops.md)
* [Tabellen kopieringsåtgärder](cassandra-spark-table-copy-ops.md)

