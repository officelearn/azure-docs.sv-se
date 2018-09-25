---
title: DDL-åtgärder i Azure Cosmos DB Cassandra-API från Spark
description: Den här artikeln beskriver keyspace och tabell DDL-åtgärder mot Azure Cosmos DB Cassandra-API från Spark.
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: b1fd935de1d52aacb28df14b498954929b05b651
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951414"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>DDL-åtgärder i Azure Cosmos DB Cassandra-API från Spark

Den här artikeln beskriver keyspace och tabell DDL-åtgärder mot Azure Cosmos DB Cassandra-API från Spark.

## <a name="cassandra-api-related-configuration"></a>Cassandra API-relaterade konfiguration 

```scala
import org.apache.spark.sql.cassandra._

//datastax Spark connector
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

## <a name="keyspace-ddl-operations"></a>Keyspace DDL-åtgärder

### <a name="create-a-keyspace"></a>Skapa ett keyspace

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Verifiera i cqlsh

Kör följande kommando i cqlsh och du bör se keyspace som du skapade tidigare.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Ta bort ett keyspace

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Verifiera i cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Table DDL-åtgärder

**Att tänka på:**  

- Dataflöde kan tilldelas på tabellnivån genom att använda create table-instruktionen.  
- En partitionsnyckel kan lagra 10 GB data.  
- En post kan lagra upp till 2 MB data.  
- En partitionsnyckelintervall kan lagra flera partitionsnycklar.

### <a name="create-a-table"></a>Skapa en tabell

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Verifiera i cqlsh

Kör följande kommando i cqlsh och du bör se tabellen med namnet ”böcker: 

```bash
USE books_ks;
DESCRIBE books;
```

Etablerat dataflöde och standard TTL-värden visas inte i utdata från föregående kommando, du kan få värdena från portalen.

### <a name="alter-table"></a>ALTER table

Du kan ändra följande värden med hjälp av alter table-kommando:

* etablerat dataflöde 
* Time-to-live värde
<br>Kolumnen ändringar stöds inte för närvarande.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Ta bort tabell

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Verifiera i cqlsh

Kör följande kommando i cqlsh och du bör se tabellen ”böcker” är inte längre tillgänglig:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Nästa steg

När du har skapat keyspace och tabellen, fortsätter du till följande artiklar för CRUD-åtgärder och mer:
 
* [Skapa/Infoga-åtgärder](cassandra-spark-create-ops.md)  
* [läsåtgärder](cassandra-spark-read-ops.md)  
* [Upsert åtgärder](cassandra-spark-upsert-ops.md)  
* [Borttagningsåtgärder](cassandra-spark-delete-ops.md)  
* [Aggregeringsåtgärder](cassandra-spark-aggregation-ops.md)  
* [Tabellen kopieringsåtgärder](cassandra-spark-table-copy-ops.md)  
