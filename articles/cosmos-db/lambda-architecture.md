---
title: Lambda-arkitektur med Azure Cosmos DB och HDInsight (Apache Spark) | Microsoft Docs
description: Den här artikeln beskriver hur du implementerar en lambda-arkitektur med hjälp av Azure Cosmos DB och HDInsight Spark
keywords: Lambda-arkitektur
services: cosmos-db
author: tknandu
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ramkris
ms.openlocfilehash: 20fea7f4f4ccf852045d53ba06c3f8fcbdd1d60d
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36959839"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementera ett lambda-arkitektur på Azure-plattformen 

Lambda-arkitekturer aktivera effektiva data bearbetning av stora datamängder. Lambda-arkitekturer Använd gruppbearbetning, dataström-bearbetning och ett betjänar lager för att minimera svarstiden ingår i ett stort datafrågor. 

För att implementera ett lambda-arkitekturen i Azure, kan du kombinera följande tekniker för att påskynda realtid stordata:
* [Azure Cosmos-DB](https://azure.microsoft.com/services/cosmos-db/), branschens första globalt distribuerade och flera olika modeller database-tjänsten. 
* [Apache Spark för Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), ett ramverk för bearbetning som kör storskaliga data analytics program
* Azure Cosmos-DB [ändra feed](change-feed.md), som strömmas nya data i batch-lagret för HDInsight för att bearbeta
* Den [Spark på Azure Cosmos DB-kopplingen](spark-connector.md)

Den här artikeln beskriver grunderna i ett lambda-arkitektur baserat på den ursprungliga designen av flera lager och fördelarna med en ”rearchitected” lambda-arkitektur som förenklar åtgärder.  

Titta på följande videoklipp en översikt av lambda-arkitektur och de tillgängliga resurserna i exemplet lambda-arkitektur:

> [!VIDEO https:///channel9.msdn.com/Events/Connect/2017/T135/player]
>

## <a name="what-is-a-lambda-architecture"></a>Vad är en lambda-arkitektur?
En lambda-arkitektur är generisk, skalbara och feltoleranta data arkitektur till adressen batch- och snabbare svarstid scenarier som beskrivs [Nathan Marz](https://twitter.com/nathanmarz).

![Diagram över en lambda-arkitektur](./media/lambda-architecture/lambda-architecture-intro.png)

Källa: http://lambda-architecture.net/

De grundläggande principerna för lambda-arkitekturen beskrivs i föregående diagram enligt [ https://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Alla **data** flyttas *både* den *batch layer* och *hastighet layer*.
 2. Den **batch layer** har en master dataset (ändras, Lägg endast uppsättning rådata) och före beräknar batch-vyer.
 3. Den **betjänar layer** har batch vyer för snabb frågor. 
 4. Den **hastighet layer** kompenserar bearbetningstid (för att det fungerar lagret) och hanterar endast senaste data.
 5. Alla frågor kan lösas genom att koppla samman resultat från batch-vyer och realtid vyer eller pinga dem individuellt.

Vi kommer att kunna genomföra den här arkitekturen med endast följande vid kan läsa:

* Azure DB Cosmos-samling(ar)
* HDInsight (Apache Spark 2.1)-kluster
* Väck Connector [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Hastighet lager

Från ett operations perspektiv kan det vara en komplicerad åtagande att upprätthålla två dataströmmar samtidigt säkerställa rätt tillstånd för data. För att förenkla åtgärder, använda den [Azure Cosmos DB ändra feed stöd](change-feed.md) att tillståndet för den *batch layer* när avslöja Azure Cosmos DB ändringsloggen via den *ändra Feed API* för din *hastighet layer*.  
![Schemat betonar nya data, hastighet lager och master dataset-delen av lambda-arkitektur](./media/lambda-architecture/lambda-architecture-change-feed.png)

Vad är viktigt i dessa lager:

 1. Alla **data** pushas *endast* i Azure Cosmos DB därmed undviker du problem med flera omvandling.
 2. Den **batch layer** har en master dataset (ändras, Lägg endast uppsättning rådata) och före beräknar batch-vyer.
 3. Den **betjänar layer** beskrivs i nästa avsnitt.
 4. Den **hastighet layer** använder HDInsight (Apache Spark) om du vill läsa Azure Cosmos DB ändra feeden. På så sätt kan du spara dina data samt att fråga efter och bearbetar den samtidigt.
 5. Alla frågor kan lösas genom att koppla samman resultat från batch-vyer och realtid vyer eller pinga dem individuellt.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Exempel: Spark strukturerad strömning till en Azure Cosmos DB ändring feed
Att köra en snabb prototyp Azure Cosmos DB ändra feed som en del av den **hastighet layer**, kan testa den med hjälp av Twitter-data som en del av den [dataströmmen bearbeta ändringar med hjälp av Azure Cosmos DB ändra Feed och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)exempel. Om du vill aldrig Twitter-utdata finns kodexemplet i [dataströmmen feed från Twitter Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). Med föregående exempel du läser in Twitter-data i Azure Cosmos DB och du kan ställa in ditt kluster i HDInsight (Apache Spark) att ansluta till ändringen feed. Mer information om hur du ställer in den här konfigurationen finns [Apache Spark till Azure Cosmos DB-anslutaren](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Följande kodavsnitt visar hur du konfigurerar `spark-shell` att köra ett strukturerade direktuppspelningsjobb att ansluta till en Azure Cosmos DB ändring feed, som går igenom i realtid Twitter-dataström, om du vill utföra en körs intervall för antal.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Fullständig kodexempel finns [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), inklusive:
* [Strömmande frågan från Cosmos DB ändra Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Strömmande taggar frågan från Cosmos DB ändra Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Utdata från det här är en `spark-shell` som körs kontinuerligt ett strukturerade strömmande jobb som utför ett intervall för antal mot Twitter-data från Azure Cosmos DB ändringen feed. Följande bild visar utdata från stream jobbet och räknar intervallet.

![Utdata visar antalet intervall mot Twitter-data från Azure Cosmos DB ändra feed för strömning](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Mer information om Azure Cosmos DB ändra feed, se:

* [Arbeta med ändringen feeds stöd i Azure Cosmos DB](change-feed.md)
* [Introduktion till Azure CosmosDB ändringen Feed Processor-bibliotek](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [: Dataströmmen bearbetning Azure CosmosDB ändrar feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch och betjänar lager
Eftersom nya data har lästs in i Azure Cosmos DB (där ändra feeden används för hastighet lager), det är där den **master dataset** (en ändras, Lägg endast uppsättning rådata) finns. Från och med den här punkten, använda HDInsight (Apache Spark) före beräknings-funktioner från den **batch layer** till **betjänar layer**, enligt följande bild:

![Diagram över syntaxmarkering batch-lagret och betjänar lager av lambda-arkitektur](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Vad är viktigt i dessa lager:

 1. Alla **data** flyttas endast Azure Cosmos DB (för att undvika problem med multicast).
 2. Den **batch layer** har en master dataset (ändras, Lägg endast uppsättning rådata) som lagras i Azure Cosmos DB. Med HDI Spark beräkna du före din aggregeringar lagras i beräknade batch-vyer.
 3. Den **betjänar layer** är en Azure Cosmos-DB-databas med samlingar för master datauppsättningen och beräknade batch vyn.
 4. Den **hastighet layer** beskrivs senare i den här artikeln.
 5. Alla frågor kan besvaras genom sammanslagning av resultat från batch-vyer och realtid vyer eller pinga dem individuellt.

### <a name="code-example-pre-computing-batch-views"></a>Exempel: före computing batch-vyer
Att demonstrerar hur du kör före beräknade vyer mot din **master dataset** från Apache Spark i Azure Cosmos DB, använder du följande kodavsnitt från de bärbara datorerna [Lambda-arkitektur Rearchitected - Batch-lager ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) och [Rearchitected arkitektur för Lambda - batchen till betjänar Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). I det här scenariot Använd Twitter-data som lagras i Azure Cosmos DB.

Börja med att skapa konfiguration anslutningen till Twitter-data i Azure Cosmos-databasen med PySpark-koden nedan.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Sedan kör vi följande Spark SQL-instruktion för att fastställa topp 10 hash-taggar för en uppsättning tweets. För den här Spark SQL-frågan kör vi det i en Jupyter-anteckningsbok utan stapeldiagrammet utdata direkt efter det här kodstycket.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Diagram som visar antalet tweets per hashtaggar](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Nu när du har din fråga kan vi spara det en samling med hjälp av Spark-anslutningen för att spara data till en annan samling.  I det här exemplet använder du Scala för att framhålla anslutningen. Liknar föregående exempel, skapa configuration-anslutning att spara Apache Spark DataFrame till en annan Azure DB som Cosmos-samling.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

När du har angett den `SaveMode` (som anger om du vill `Overwrite` eller `Append` dokument), skapa en `tweets_bytags` DataFrame liknar Spark SQL-frågan i föregående exempel.  Med den `tweets_bytags` DataFrame skapats kan du spara den med hjälp av den `write` metod som använder den tidigare angivna `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Det här sista instruktionen nu har sparats Spark-DataFrame till en ny Azure DB som Cosmos-samling; ur ett lambda-arkitektur, detta är din **batch-vyn** inom den **betjänar layer**.
 
#### <a name="resources"></a>Resurser

Fullständig kodexempel finns [azure-cosmosdb-spark/lambda/samples](vhttps://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) inklusive:
* Lambda-arkitektur Rearchitected - Batch Layer [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda-arkitektur Rearchitected - Batch betjänar lagret [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Hastighet lager
Som tidigare meddelanden, som använder Azure Cosmos DB ändra Feed biblioteket kan du förenkla åtgärder mellan batch- och lager. I den här arkitekturen använda Apache Spark (via HDInsight) att utföra den *strukturerad strömning* frågor mot data. Du kanske också vill temporärt spara resultatet av dina strukturerade strömmande frågor så att andra system kan komma åt dessa data.

![Schemat betonar hastighet lager av lambda-arkitektur](./media/lambda-architecture/lambda-architecture-speed.png)

Gör du genom att skapa en separat Azure DB som Cosmos-samling om du vill spara resultatet av dina strukturerade strömmande frågor.  Detta kan du ha andra system åtkomst informationen inte bara Apache Spark. Du kan också konfigurera dokument ska tas bort automatiskt efter en angiven varaktighet med funktionen Time-to-Live (TTL) Cosmos DB.  Läs mer om Azure Cosmos DB TTL-funktionen [ut data i Azure Cosmos DB samlingar automatiskt med time to live-](time-to-live.md)

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Lambda-arkitektur: Rearchitected
Enligt beskrivningen i föregående avsnitt, kan du förenkla den ursprungliga lambda-arkitekturen med hjälp av följande komponenter:
* Azure Cosmos DB
* Azure Cosmos DB ändra Feed biblioteket för att undvika att behöva multicast data mellan lager batch och hastighet
* Apache Spark i HDInsight
* Spark-anslutningen till Azure Cosmos DB

![Diagram över rearchitecture av lambda-arkitektur med Azure Cosmos DB, Spark och Azure Cosmos DB ändra Feed API](./media/lambda-architecture/lambda-architecture-re-architected.png)

Med den här designen behöver du bara två hanterade tjänster, Azure Cosmos DB och HDInsight. De riktar tillsammans, batch, fungerar och hastighet lager av lambda-arkitekturen. Detta förenklar inte bara åtgärderna utan också dataflödet. 
 1. Alla data skickas till Azure Cosmos DB för bearbetning
 2. Batch-lagret har en master dataset (ändras, Lägg endast uppsättning rådata) och före beräknar batch-vyer
 3. Betjänar lagret har batch vyer av data för snabba frågor.
 4. Hastighet lagret kompenserar bearbetningstid (för att det fungerar lagret) och hanterar endast senaste data.
 5. Alla frågor kan lösas genom att kombinera resultat från batch-vyer och realtid vyer.

### <a name="resources"></a>Resurser

 * **Nya data**: den [dataströmmen feed från Twitter till CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), vilket är en mekanism för att skicka nya data till Azure Cosmos DB.
 * **Batch-lager:** batch-lagret består av den *master dataset* (en ändras, Lägg endast uppsättning rådata) och möjligheten att beräkna före batch vyer av data som skickas till den **betjänar lager** .
    * Den **Lambda-arkitektur Rearchitected - Batch Layer** anteckningsboken [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) frågor i *master dataset* uppsättning batch vyer.
 * **Betjänar lager:** den **betjänar layer** består av förberäknade data, vilket resulterar i batch-vyer (till exempel aggregeringar, specifika utsnitt o.s.v.) för snabb frågor.
    * Den **Lambda-arkitektur Rearchitected - Batch betjänar lagret** anteckningsboken [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) skickar batch-data i lagret betjänar; som är Väck frågar en batch uppsättning tweets, bearbetar och lagrar den i en annan samling (en beräknad batch).
* **Hastighet lager:** den **hastighet layer** består av Spark som använder Azure Cosmos DB ändringen feed Läs-och agera på omedelbart. Data kan också sparas på *beräknad RT* så att andra system kan fråga bearbetade realtidsdata och kör en realtid fråga sig själva.
    * Den [strömning frågan från Cosmos DB ändra Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala skriptet körs en strömmande fråga från Azure Cosmos DB ändringen feed för att beräkna ett intervall för antal i spark-gränssnittet.
    * Den [strömning taggar frågan från Cosmos DB ändra Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala skriptet körs en strömmande fråga från Azure Cosmos DB ändringen feed för att beräkna ett intervall för antal taggar i spark-gränssnittet.
  
## <a name="next-steps"></a>Nästa steg
Om du inte redan gjort ladda ned Spark till Azure DB som Cosmos-anslutningen från den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-lagringsplatsen och utforska ytterligare resurser i lagringsplatsen:
* [Lambda-arkitektur](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Distribuerade aggregeringar exempel](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Exempel på skript och bärbara datorer](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Strukturerade strömmande demonstrationer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Ändra feed demonstrationer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Bearbetning av ändringar med hjälp av Azure Cosmos DB ändra Feed och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Du kanske också vill granska den [datauppsättningar Guide, DataFrames och Apache Spark SQL](http://spark.apache.org/docs/latest/sql-programming-guide.html) och [Apache Spark på Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artikel.
