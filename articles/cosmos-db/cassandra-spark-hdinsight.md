---
title: Få tillgång till Azure Cosmos DB Cassandra API från Spark på YARN med HDInsight
description: Den här artikeln beskriver hur du arbetar med Azure Cosmos DB Cassandra API från Spark on YARN med HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bef4ee14cb4a7d64d80dc5776d8ecea0f831881a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887640"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Få tillgång till Azure Cosmos DB Cassandra API från Spark på YARN med HDInsight

Den här artikeln beskriver hur du kommer åt Azure Cosmos DB Cassandra API från Spark på YARN med HDInsight-Spark från spark-shell. HDInsight är Microsofts Hortonworks Hadoop PaaS på Azure som utnyttjar objektlagring för HDFS och finns i flera smaker, inklusive [Spark](../hdinsight/spark/apache-spark-overview.md).  Medan innehållet i det här dokumentet refererar till HDInsight-Spark, gäller det för alla Hadoop-distributioner.  

## <a name="prerequisites"></a>Krav

* [Etablera Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [Granska grunderna för att ansluta till Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* [Etablera ett HDInsight-Spark-kluster](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Granska kodexemplen för att arbeta med Cassandra API](cassandra-spark-generic.md#next-steps)

* [Använd cqlsh för validering om du så föredrar](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra API-konfiguration i Spark2** - Spark-anslutningen för Cassandra kräver att Cassandra-anslutningsinformationen initieras som en del av Spark-kontexten. När du startar en Jupyter-anteckningsbok initieras redan miniatyrsessionen och kontexten och det är inte tillrådligt att stoppa och initiera Spark-kontexten igen om den inte är komplett med varje konfigurationsuppsättning som en del av hdinsight-standardstarten för Jupyter-anteckningsboken för Jupyter. En lösning är att lägga till Cassandra-instansinformationen i Ambari, Spark2-tjänstkonfigurationen direkt. Det här är en engångsaktivitet per kluster som kräver omstart av Spark2-tjänsten.
 
  1. Gå till Ambari, Spark2-tjänsten och välj configs

  2. Gå sedan till anpassade spark2-standardvärden och lägg till en ny egenskap med följande och starta om Spark2-tjänsten:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Få tillgång till Azure Cosmos DB Cassandra API från Spark-skalet

Spark shell används för testning / prospektering.

* Starta spark-shell med de maven-beroenden som krävs som är kompatibla med klustrets Spark-version.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Utföra vissa DDL- och DML-åtgärder

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

* Kör CRUD-operationer

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

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Få tillgång till Azure Cosmos DB Cassandra API från Jupyter-anteckningsböcker

HDInsight-Spark levereras med Zeppelin och Jupyter bärbara tjänster. De är båda webbaserade miljöer för bärbara datorer som stöder Scala och Python. Bärbara datorer är bra för interaktiva utforskande analyser och samarbeten, men inte avsedda för operativa/produktionsiserade processer.

Följande Jupyter-anteckningsböcker kan överföras till ditt HDInsight Spark-kluster och tillhandahålla färdiga exempel för att arbeta med Azure Cosmos DB Cassandra API. Var noga med att `1.0-ReadMe.ipynb` granska den första anteckningsboken för att granska Spark-tjänstkonfigurationen för anslutning till Azure Cosmos DB Cassandra API.

Ladda ned dessa anteckningsböcker under [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) till din dator.
  
### <a name="how-to-upload"></a>Så här laddar du upp:
När du startar Jupyter navigerar du till Scala. Skapa en katalog först och ladda sedan upp anteckningsböckerna till katalogen. Uppladdningsknappen är på övre, högra sidan.  

### <a name="how-to-run"></a>Så här kör du:
Kör igenom anteckningsböckerna och varje anteckningsbokscell sekventiellt.  Klicka på körningsknappen högst upp i varje anteckningsbok för att köra alla celler, eller skift+enter för varje cell.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Åtkomst med Azure Cosmos DB Cassandra API från ditt Spark Scala-program

För automatiserade processer i produktion skickas Spark-program till klustret via [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Nästa steg

* [Hur man bygger ett Spark Scala-program i ett IDE och skickar det till HDInsight Spark-klustret genom Livy för körning](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Så här ansluter du till Azure Cosmos DB Cassandra API från ett Spark Scala-program](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Fullständig lista över kodexempel för arbete med Cassandra API](cassandra-spark-generic.md)
