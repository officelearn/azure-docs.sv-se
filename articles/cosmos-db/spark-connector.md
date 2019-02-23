---
title: Apache Spark ansluta till Azure Cosmos DB
description: Läs mer om Azure Cosmos DB Spark-anslutningsappen som gör det möjligt att ansluta Apache Spark i Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: ramkris
ms.openlocfilehash: 99c0675f8dc7f392e6c98abbe43b84eb14dbddbc
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675355"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Påskynda analyser av stordata med Apache Spark för Azure Cosmos DB-anslutningsapp

Du kan köra [Spark](https://spark.apache.org/) jobb med data som lagras i Azure Cosmos DB med Cosmos DB Spark-anslutningsappen. Cosmos kan användas för batch- och bearbetning av dataströmmen och som en betjäningslagret för låglatensåtkomst.

Du kan använda anslutningen med [Azure Databricks](https://azure.microsoft.com/services/databricks) eller [Azure HDInsight, som ger hanterad Spark-kluster på Azure. I följande tabell visas stöds Spark versioner.

| Komponent | Version |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x och 2.1.x |
| Scala | 2.11 |
| Azure Databricks-körningsversion | > 3.4 |

> [!WARNING]
> Core (SQL)-API: et för Azure Cosmos DB stöder den här anslutningen.
> Cosmos DB för MongoDB-API kan använda den [MongoDB Spark-anslutningsappen](https://docs.mongodb.com/spark-connector/master/).
> Cosmos DB Cassandra-API, använda den [Cassandra Spark-anslutningsappen](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Snabbstart

* Följ stegen i [Kom igång med Java SDK](sql-api-async-java-get-started.md) att ställa in en Cosmos DB-konto och fylla i vissa data.
* Följ stegen i [Azure Databricks har börjat](https://docs.azuredatabricks.net/getting-started/index.html) du ställer in en Azure Databricks-arbetsytan och kluster.
* Du kan nu skapa nya, anteckningsböcker och importera kopplingsbibliotek Cosmos DB. Gå till [arbeta med Cosmos DB-anslutningsapp](#bk_working_with_connector) mer information om hur du ställer in din arbetsyta.
* Följande avsnitt har kodfragment på läsa och skriva med hjälp av anslutningstjänsten.

### <a name="reading-from-cosmos-db"></a>Läsning från Cosmos DB

Följande kodfragment visar hur du skapar en Spark DataFrame att läsa från Cosmos DB i PySpark.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

Och samma kodfragmentet i Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
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

### <a name="writing-to-cosmos-db"></a>Skrivning till Cosmos DB

Följande kodfragment visar hur du skriver en dataram till Cosmos DB i PySpark.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

Och samma kodfragmentet i Scala:

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Mer kodfragment och heltäckande exempel finns i [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a> Arbeta med anslutningen

Du kan skapa anslutning från källa i Github eller hämta uber JAR-filer från Maven med länkarna nedan.

| Spark | Scala | Senaste versionen |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2)

### <a name="using-databricks-notebooks"></a>Med Databricks-anteckningsböcker

Skapa ett bibliotek med inom din Databricks-arbetsyta genom att följa vägledningen i Azure Databricks-guiden > [använder Spark för Azure Cosmos DB-anslutningen](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Observera att den **använder Azure Cosmos DB Spark Connector** sidan är för närvarande inte uppdaterad. Istället för att hämta de sex separata JAR-filer i sex olika bibliotek, du kan hämta uber JAR-filen från maven på https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar) och installera det här en jar/biblioteket.
> 

### <a name="using-spark-cli"></a>Med hjälp av spark-cli

Att arbeta med med hjälp av spark-cli (det vill säga `spark-shell`, `pyspark`, `spark-submit`), du kan använda den `--packages` parameter med kopplingens [maven-koordinater](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>Med Jupyter-anteckningsböcker

Om du använder en Jupyter-anteckningsböcker i HDInsight kan du använda spark-magic `%%configure` cell ange kopplingens maven-koordinater.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Anteckning, inkludering av den `spark.jars.excludes` är specifikt för att ta bort konflikter mellan connector, Apache Spark och Livy.

### <a name="build-the-connector"></a>Skapa anslutningen

Det här projektet för anslutningen används för närvarande, `maven` så att du kan köra för att skapa utan beroenden:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Arbeta med våra exempel

Den [Cosmos DB Spark GitHub-lagringsplatsen](https://github.com/Azure/azure-cosmosdb-spark) har följande exempelanteckningsböcker och skript som du kan prova.

* **Flight Punktlighetsinformation med Spark och Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Anslut Spark till Cosmos DB med HDInsight Jupyter notebook-tjänst för att demonstrera Spark SQL, GraphFrames och att förutsäga flygförseningar med hjälp av ML-pipelines.
* **[Ansluta Spark med Cosmos DB-Ändringsflödet](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)**: En snabb showcase på hur du ansluter Spark till Cosmos DB Change Feed.
* **Twitter-källkod med Apache Spark och Azure Cosmos DB Change Feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Använda Apache Spark för att fråga Cosmos DB diagram**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Ansluta Azure Databricks till Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  med `azure-cosmosdb-spark`.  Länkad här finns också en Azure Databricks-version av den [flygning Punktlighetsinformation notebook](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Lambda-arkitektur med Azure Cosmos DB och HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Du kan minska arbetet med att underhålla big datapipelines med Cosmos DB- och Spark.

## <a name="more-information"></a>Mer information

Vi har mer information i den `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) inklusive:

* [Användarhandbok för Azure Cosmos DB Spark Connector](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Aggregeringar exempel](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfiguration och installation

* [Spark-Kopplingskonfiguration](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark kan installationsprogrammet för Cosmos DB Connector](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (pågår)
* [Konfigurera Power BI Direct Query till Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Felsökning

* [Med Cosmos DB-samlingar](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Kända problem](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Prestanda

* [Prestandatips](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Fråga Testkörningar](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Skriva Testkörningar](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Ändringsfeed

* [Stream bearbetning av ändringar med hjälp av Azure Cosmos DB Change Feed och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Ändra Feed demonstrationer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Strukturerade Stream-demonstrationer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Övervakning

* [Övervakning av Spark-jobb med application insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Nästa steg

Om du inte redan gjort hämta Spark till Azure Cosmos DB-anslutningen från den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-lagringsplatsen. Utforska följande ytterligare resurser i lagringsplatsen:

* [Aggregeringar exempel](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exempel på skript och anteckningsböcker](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Du kan också gå igenom den [Apache Spark SQL och DataFrames datauppsättningar Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html), och [Apache Spark på Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artikeln.
