---
title: Åtkomst till Azure Cosmos DB Cassandra-API från Spark på YARN med HDInsight
description: Den här artikeln beskriver hur du arbetar med Azure Cosmos DB Cassandra-API från Spark på YARN med HDInsight
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: a86b7375122d38d36707a419b265dcd3c1b57087
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741699"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Åtkomst till Azure Cosmos DB Cassandra-API från Spark på YARN med HDInsight

Den här artikeln beskriver hur du kommer åt Azure Cosmos DB Cassandra-API från Spark på YARN med HDInsight Spark från spark-shell. HDInsight är Microsofts Hortonworks Hadoop PaaS på Azure som utnyttjar objektlagring för HDFS och ingår i flera varianter inklusive [Spark](../hdinsight/spark/apache-spark-overview.md).  Innehållet i det här dokumentet refererar till HDInsight Spark, är det gäller för alla Hadoop-distributioner.  

## <a name="prerequisites"></a>Förutsättningar

* [Etablera Azure Cosmos DB Cassandra-API](create-cassandra-dotnet.md#create-a-database-account)

* [Gå igenom grunderna för att ansluta till Azure Cosmos DB Cassandra-API](cassandra-spark-generic.md)

* [Etablera ett HDInsight Spark-kluster](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Granska kodexempel för att arbeta med API för Cassandra](cassandra-spark-generic.md#next-steps)

* [Använd cqlsh för verifiering om du så föredrar](cassandra-spark-generic.md##connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra-API-konfiguration i Spark2** – The Spark-anslutningsappen för Cassandra kräver att Cassandra-anslutning information om för att initiera som en del av Spark-kontext. När du startar en Jupyter notebook i spark-session och på snabbmenyn har redan initierats och det rekommenderas inte att stoppa och initiera om Spark-kontexten, såvida inte den är klar med varje konfigurationen som har angetts som en del av HDInsight standard Jupyter notebook start. En lösning är att lägga till Cassandra-instansinformation Ambari, Spark2 tjänstkonfiguration direkt. Detta inträffar en gång per kluster som kräver Spark2 tjänsten startas om.
 
  1. Gå till Ambari, Spark2-tjänsten och väljer konfigurationer

  2. Sedan går du till anpassade spark2-standardinställningar och lägga till en ny egenskap med följande och starta om Spark2-tjänsten:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Åtkomst till Azure Cosmos DB Cassandra API från Spark shell

Spark-shell används för testning/utforskning.

* Starta spark-shell med de nödvändiga maven-beroendena som är kompatibel med Spark-version för ditt kluster.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Köra vissa DDL och DML-åtgärder

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* Köra CRUD-åtgärder

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Åtkomst till Azure Cosmos DB Cassandra API från Jupyter-anteckningsböcker

HDInsight Spark kan integreras med Zeppelin och Jupyter notebook-tjänster. De är båda webbaserade notebook-miljöer som har stöd för Scala och Python. Anteckningsböcker är bra för interaktiva och undersökande analyser och samarbete, men tänkt inte för operativa/productionized processer.

Följande Jupyter notebooks kan överföras till ditt HDInsight Spark-kluster och ger redo-exempel för att arbeta med Azure Cosmos DB Cassandra-API. Se till att granska första anteckningsboken `1.0-ReadMe.ipynb` att granska Spark-tjänstkonfigurationen för att ansluta till Azure Cosmos DB Cassandra-API.

Ladda ned dessa anteckningsböcker under [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) till din dator.
  
### <a name="how-to-upload"></a>Hur du laddar upp:
När du startar Jupyter, navigera till Scala. Skapa en katalog först och sedan ladda upp anteckningsböckerna till katalogen. Knappen ladda upp är längst upp höger sida.  

### <a name="how-to-run"></a>Så här kör du:
Gå igenom de bärbara datorerna och varje cell i anteckningsboken sekventiellt.  Klicka på knappen Kör överst i varje anteckningsboken för att köra alla celler eller SKIFT + enter för varje cell.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Åtkomst med Azure Cosmos DB Cassandra-API från Spark Scala-program

För automatiserade processer i produktion skickas Spark-program till klustret via [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Nästa steg

* [Hur du skapar ett Spark Scala programmet från IDE-miljö och skicka den till HDInsight Spark-kluster via Livy för körning](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Hur du ansluter till Azure Cosmos DB Cassandra-API från ett Scala Spark-program](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Fullständig lista över kodexempel för att arbeta med API för Cassandra](cassandra-spark-generic.md)
