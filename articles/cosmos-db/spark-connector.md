---
title: Anslut Apache Spark till Azure Cosmos DB
description: Lär dig mer om Azure Cosmos DB Spark-kopplingen som gör att du kan ansluta Apache Spark till Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: edfaf50b701f64b12f9cf5fcc9ab8d2c6d241d0a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482180"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Påskynda stordataanalys med hjälp av Apache Spark till Azure Cosmos DB-anslutning

Du kan köra [Spark-jobb](https://spark.apache.org/) med data som lagras i Azure Cosmos DB med Cosmos DB Spark-kopplingen. Cosmos kan användas för batch- och dataflödesbearbetning och som ett serveringslager för låg latensåtkomst.

Du kan använda kopplingen med [Azure Databricks](https://azure.microsoft.com/services/databricks) eller [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), som tillhandahåller hanterade Spark-kluster på Azure. I följande tabell visas Spark-versioner som stöds.

| Komponent | Version |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x och 2.1.x |
| Scala | 2.11 |
| Azure Databricks runtime version | > 3,4 |

> [!WARNING]
> Den här anslutningen stöder core -API:et (SQL) i Azure Cosmos DB.
> Använd [MongoDB Spark-anslutningen](https://docs.mongodb.com/spark-connector/master/)för Cosmos DB för MongoDB API .
> Använd [Cassandra Spark-kontakten](https://github.com/datastax/spark-cassandra-connector)för Cosmos DB Cassandra API .
>

## <a name="quickstart"></a>Snabbstart

* Följ stegen på [Kom igång med Java SDK](sql-api-async-java-get-started.md) för att konfigurera ett Cosmos DB-konto och fylla i vissa data.
* Följ stegen i [Azure Databricks som kommer igång](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) för att konfigurera en Azure Databricks-arbetsyta och ett kluster.
* Du kan nu skapa nya anteckningsböcker och importera Cosmos DB-anslutningsbiblioteket. Gå till [Arbeta med Cosmos DB-kopplingen](#bk_working_with_connector) för mer information om hur du konfigurerar arbetsytan.
* I följande avsnitt finns utdrag om hur du läser och skriver med kopplingen.

### <a name="batch-reads-from-cosmos-db"></a>Batch läser från Cosmos DB

Följande kodavsnitt visar hur du skapar en Spark DataFrame att läsa från Cosmos DB i PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

Och samma kodavsnitt i Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Batch skriver till Cosmos DB

Följande kodavsnitt visar hur du skriver en dataram till Cosmos DB i PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

Och samma kodavsnitt i Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Streaming läser från Cosmos DB

Följande kodavsnitt visar hur du ansluter till och läser från Azure Cosmos DB Change Feed.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
Och samma kodavsnitt i Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Streaming skriver till Cosmos DB

Följande kodavsnitt visar hur du skriver en dataram till Cosmos DB i PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

Och samma kodavsnitt i Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Fler utdrag och end to end-prover, se [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>Arbeta med kontakten

Du kan skapa kopplingen från källan i GitHub, eller ladda ner uber burkar från Maven i länkarna nedan.

| Spark | Scala | Senaste versionen |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Använda databricks-anteckningsböcker

Skapa ett bibliotek med hjälp av din Databricks-arbetsyta genom att följa vägledningen i Azure Databricks Guide > [Använda Azure Cosmos DB Spark-kopplingen](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Sidan **Använd Azure Cosmos DB Spark Connector** är för närvarande inte uppdaterad. Istället för att ladda ner de sex separata burkarna till sex olika https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) bibliotek kan du ladda ner uber-burken från maven på och installera den här burken/biblioteket.
> 

### <a name="using-spark-cli"></a>Använda spark-cli

Om du vill arbeta med kontakten med `spark-shell`hjälp `pyspark` `spark-submit`av spark-cli `--packages` (det vill säga , , , ) kan du använda parametern med kopplingens [maven-koordinater](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Använda Jupyter-anteckningsböcker

Om du använder Jupyter-anteckningsböcker i HDInsight kan `%%configure` du använda sparkmagicell för att ange kontaktens maven-koordinater.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Obs, införandet av `spark.jars.excludes` är specifik för att ta bort potentiella konflikter mellan kopplingen, Apache Spark och Livy.

### <a name="build-the-connector"></a>Skapa kopplingen

För närvarande använder `maven` det här anslutningsprojektet så att det skapas utan beroenden kan du köra:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Arbeta med våra prover

[Cosmos DB Spark GitHub-databasen](https://github.com/Azure/azure-cosmosdb-spark) har följande exempel anteckningsböcker och skript som du kan prova.

* **On-Time Flight Performance med Spark och Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html:](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)Anslut Spark till Cosmos DB med HDInsight Jupyter notebook service för att visa upp Spark SQL, GraphFrames och förutsäga flygförseningar med ML-pipelines.
* **Twitter Källa med Apache Spark och Azure Cosmos DB Ändra Feed:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Använda Apache Spark att fråga Cosmos DB Grafer:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Ansluta Azure Databricks till Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** med `azure-cosmosdb-spark`.  Länkad här är också en Azure Databricks-version av [notebooken On-Time Flight Performance](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Lambda-arkitektur med Azure Cosmos DB och HDInsight (Apache Spark):](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** Du kan minska driftkostnaderna för att underhålla stordatapipelpipelsar med Cosmos DB och Spark.

## <a name="more-information"></a>Mer information

Vi har mer `azure-cosmosdb-spark` information i [wikin](https://github.com/Azure/azure-cosmosdb-spark/wiki) inklusive:

* [Användarhandbok för Azure Cosmos DB Spark Connector](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Exempel på aggregeringar](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfiguration och installation

* [Konfiguration av Spark Connector](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark till Cosmos DB Connector Setup](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (Pågår)
* [Konfigurera Power BI Direct-fråga till Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Felsökning

* [Använda Cosmos DB-aggregat](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Kända problem](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Prestanda

* [Tips för prestanda](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Frågetestkörningar](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Skriva testkörningar](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Ändringsflöde

* [Strömma bearbetningsändringar med Azure Cosmos DB Change Feed och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Ändra feeddemos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demonstrationer för strukturerad ström](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Övervakning

* [Övervaka Spark-jobb med programinsikter](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det hämtar du Spark till Azure Cosmos [DB-anslutningen från Azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-databasen. Utforska följande ytterligare resurser i repoet:

* [Exempel på aggregeringar](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exempel på skript och anteckningsböcker](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Du kanske också vill granska [Apache Spark SQL, DataFrames och Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)och Apache Spark på Azure [HDInsight-artikeln.](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
