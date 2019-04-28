---
title: Lambda-arkitektur med Azure Cosmos DB och HDInsight (Apache Spark)
description: Den här artikeln beskriver hur du implementerar en lambda-arkitektur med Azure Cosmos DB och HDInsight Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 6902b1a26d02efbf1a31fe9a3a25253a6b5a5604
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043828"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementera en lambda-arkitektur på Azure-plattformen 

Lambda-arkitekturer aktivera effektiv databearbetning massiva datauppsättningar. Lambda-arkitektur använda batch-bearbetning, strömbearbetning och en betjäningslagret för att minimera fördröjning som ingår i fråga stordata. 

Du kan kombinera följande tekniker för att påskynda analyser av stordata i realtid för att implementera en lambda-arkitektur på Azure:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), branschens första globalt distribuerad databastjänst. 
* [Apache Spark för Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), ett bearbetningsramverk som kör storskaliga dataanalysprogram
* Azure Cosmos DB [ändringsflödet](change-feed.md), som strömmar nya data i batch-lagret för HDInsight att bearbeta
* Den [Spark till Azure Cosmos DB-anslutningstjänsten](spark-connector.md)

Den här artikeln beskriver grunderna i en lambda-arkitekturen baserat på den ursprungliga designen i flera lager och fördelarna med en ”rearchitected” lambda-arkitektur som förenklar åtgärder.  

## <a name="what-is-a-lambda-architecture"></a>Vad är en lambda-arkitekturen?
En lambda-arkitekturen är generisk, skalbar och feltolerant databearbetning arkitektur till adressen batch- och snabba svarstider scenarier som beskrivs av [Nathan Marz](https://twitter.com/nathanmarz).

![Diagram som visar en lambda-arkitektur](./media/lambda-architecture/lambda-architecture-intro.png)

Källa: http://lambda-architecture.net/

De grundläggande principerna för en lambda-arkitekturen beskrivs i föregående diagram enligt [ http://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Alla **data** pushas till *både* den *batchlager* och *hastighetslagret*.
 2. Den **batchlager** har en master datauppsättning (inte kan ändras, Lägg endast uppsättning rådata) och beräkningar före batch-vyer.
 3. Den **betjäningslagret** har batch-vyer för snabba frågor. 
 4. Den **hastighetslagret** kompenserar för bearbetningstid (för att betjäningslagret) och hanterar endast senaste data.
 5. Alla frågor besvaras genom att koppla samman resultat från batch-vyer och i realtid vyer eller pinga dem individuellt.

Vid läsning av ytterligare, kommer vi att kunna genomföra den här arkitekturen använder endast följande:

* Azure Cosmos DB-samling(ar)
* Kluster för HDInsight (Apache Spark 2.1)
* Spark-Anslutningappen [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Hastighetslagret

En ur åtgärder kan det vara en komplicerad åtagande att upprätthålla två dataströmmar som säkerställer att rätt tillstånd för data. För att förenkla åtgärder kan använda den [stöd för Azure Cosmos DB-ändringsfeed](change-feed.md) att tillståndet för den *batchlager* vid avslöja Ändringslogg för Azure Cosmos DB via den *ändra Feed API* för din *hastighetslagret*.  
![Diagram över fokus på den nya data, hastighetslagret och master datauppsättning delen av lambda-arkitekturen](./media/lambda-architecture/lambda-architecture-change-feed.png)

Vad är viktigt i dessa lager:

 1. Alla **data** pushas *endast* till Azure Cosmos DB, vilket du kan undvika att flera omvandling problem.
 2. Den **batchlager** har en master datauppsättning (inte kan ändras, Lägg endast uppsättning rådata) och beräkningar före batch-vyer.
 3. Den **betjäningslagret** beskrivs i nästa avsnitt.
 4. Den **hastighetslagret** använder HDInsight (Apache Spark) om du vill läsa Azure Cosmos DB-ändringsflödet. På så sätt kan du spara filer samt att fråga och bearbeta dem samtidigt.
 5. Alla frågor besvaras genom att koppla samman resultat från batch-vyer och i realtid vyer eller pinga dem individuellt.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Kodexempel: Spark-strukturerad direktuppspelning till en Azure Cosmos DB-ändringsflödet
Att köra en snabb prototyp av Azure Cosmos DB-ändringsflödet som en del av den **hastighetslagret**, testa den ut med Twitter-data som en del av den [Stream bearbetning av ändringar med hjälp av Azure Cosmos DB ändra Feed och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)exempel. Om du vill ge din Twitter-utdata, finns i kodexemplet i [Stream feed från Twitter till Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). Du läser in Twitter-data till Azure Cosmos DB och du kan ställa in ditt kluster i HDInsight (Apache Spark) att ansluta till den ändringsflödet med föregående exempel. Mer information om hur du ställer in den här konfigurationen finns i [Apache Spark för Azure Cosmos DB-anslutaren](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Följande kodfragment visar hur du konfigurerar `spark-shell` att köra ett strukturerad direktuppspelning jobb att ansluta till en Azure Cosmos DB-ändringsflödet, som går igenom i realtid Twitter dataströmmen, om du vill utföra en löpande uppräkning av intervall.

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

Fullständig kodexempel, se [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), inklusive:
* [Strömmande frågan från Cosmos DB ändra Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Strömmande taggar frågan från Cosmos DB ändra Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Utdata från det här är en `spark-shell` som körs kontinuerligt ett strukturerad direktuppspelning jobb som utför ett intervall för antal mot Twitter-data från Azure Cosmos DB-ändringsflödet. Följande bild visar utdata från stream-jobbet och intervallet räknas.

![Utdata visar intervallantalet mot Twitter-data från Azure Cosmos DB-ändringsflödet för direktuppspelning](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Mer information om Azure Cosmos DB-ändringsflödet, se:

* [Arbeta med stöd för ändringsflödet i Azure Cosmos DB](change-feed.md)
* [Introduktion till Azure CosmosDB ändringen Feed Processor-biblioteket](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Stream bearbeta ändringar: Azure cosmos DB-ändringsflödet + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch- och betjänar lager
Eftersom den nya informationen läses in i Azure Cosmos DB (där ändringsflöde används för hastighetslagret) och det är där den **master datauppsättning** (en oföränderlig, Lägg endast uppsättning rådata) finns. Från och med den här punkten, använda HDInsight (Apache Spark) för att utföra före beräknings-funktioner från den **batchlager** till **betjäningslagret**, enligt följande bild:

![Diagram som visar batchlager och betjäningslagret med lambda-arkitekturen](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Vad är viktigt i dessa lager:

 1. Alla **data** skickas endast till Azure Cosmos DB (för att undvika multicast problem).
 2. Den **batchlager** har en master datauppsättning (inte kan ändras, Lägg endast uppsättning rådata) som lagras i Azure Cosmos DB. Med HDI Spark kan beräkna du förväg din aggregeringar lagras i dina beräknade batch-vyer.
 3. Den **betjäningslagret** är en Azure Cosmos DB-databas med samlingar för master datauppsättningen och beräknad batchvyn.
 4. Den **hastighetslagret** beskrivs senare i den här artikeln.
 5. Alla frågor besvaras genom att sammanfoga resultatet från batch-vyer i realtid vyer, pinga dem individuellt.

### <a name="code-example-pre-computing-batch-views"></a>Kodexempel: Före databehandling batch-vyer
Att demonstrera hur du kör förberäknade vyer mot din **master datauppsättning** från Apache Spark i Azure Cosmos DB, använder du följande kodavsnitt från de bärbara datorerna [Lambda-arkitektur Rearchitected - Batchlager ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) och [Lambda-arkitekturen Rearchitected - Batch för att serva Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). I det här scenariot använder du Twitter-data som lagras i Azure Cosmos DB.

Låt oss börja med att skapa konfigurationen anslutningen till Twitter-data i Azure Cosmos DB med hjälp av PySpark-kod nedan.

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

Sedan kör vi följande Spark SQL-instruktion för att fastställa de översta 10 hashtaggar av en uppsättning tweets. Den här Spark SQL-frågan är kör vi Jupyter notebook utan stapeldiagrammet utdata direkt efter det här kodfragmentet.

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

![Diagram som visar antal tweets per hashtagg](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Nu när du har din fråga kan vi spara tillbaka dem till en samling med hjälp av Spark-anslutningen för att spara utdata till en annan samling.  I det här exemplet använder du Scala för att demonstrera anslutningen. Ett liknande sätt som i föregående exempel, skapa konfigurationsanslutning för att spara Apache Spark DataFrame till en annan Azure Cosmos DB-samling.

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

När du har angett den `SaveMode` (som anger om du vill `Overwrite` eller `Append` dokument), skapa en `tweets_bytags` DataFrame liknar Spark SQL-fråga i föregående exempel.  Med den `tweets_bytags` DataFrame skapats, kan du spara den med hjälp av den `write` metoden med hjälp av den tidigare angivna `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Det här sista instruktionen nu har sparats Spark DataFrame till en ny Azure Cosmos DB-samling; ur en lambda-arkitektur, det här är din **batchvy** inom den **betjäningslagret**.
 
#### <a name="resources"></a>Resurser

Fullständig kodexempel, se [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) inklusive:
* Lambda-arkitektur Rearchitected - Batchlager [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda-arkitektur Rearchitected - Batch betjänar lagret [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Hastighetslagret
Som tidigare meddelanden, som använder Azure Cosmos DB ändringen Feed-biblioteket kan du förenkla åtgärder mellan batch och hastighet lager. I den här arkitekturen använda Apache Spark (via HDInsight) för att utföra den *strukturerad direktuppspelning* frågor mot data. Du kanske också vill temporärt spara resultatet av dina strukturerad direktuppspelning frågor så att andra system kan komma åt dessa data.

![Schemat betonar hastighetslagret av lambda-arkitekturen](./media/lambda-architecture/lambda-architecture-speed.png)

Gör detta genom att skapa en separat Azure Cosmos DB-samling för att spara resultatet av dina strukturerad direktuppspelning frågor.  Detta gör att du kan ha andra system åtkomst den här informationen inte bara Apache Spark. Du kan också konfigurera dina dokument som ska tas bort automatiskt efter en angiven varaktighet med funktionen Time-to-Live (TTL) Cosmos DB.  Läs mer om Azure Cosmos DB TTL-funktionen [ta bort data från Azure Cosmos DB-samlingarna automatiskt med TTL-värde](time-to-live.md)

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
Enligt vad som anges i föregående avsnitt, kan du förenkla ursprungliga lambda-arkitekturen med hjälp av följande komponenter:
* Azure Cosmos DB
* Azure Cosmos DB ändringen Feed klientbiblioteket att undvika att behöva multicast dina data mellan batch och hastighet lager
* Apache Spark i HDInsight
* Spark-Anslutningsapp för Azure Cosmos DB

![Diagram över rearchitecture över lambda-arkitekturen med hjälp av Azure Cosmos DB-, Spark- och Azure Cosmos DB ändringen Feed-API](./media/lambda-architecture/lambda-architecture-re-architected.png)

Med den här designen behöver du bara två hanterade tjänster, Azure Cosmos DB och HDInsight. De löser tillsammans i batch och betjänar hastighet lager i lambda-arkitekturen. Detta förenklar inte bara åtgärder utan även dataflödet. 
 1. Alla data pushas till Azure Cosmos DB för bearbetning
 2. Batch-lagret har en master datauppsättning (inte kan ändras, Lägg endast uppsättning rådata) och beräkningar före batch-vyer
 3. Betjäningslagret har batch vyer av data för snabba frågor.
 4. Hastighetslagret kompenserar för bearbetningstid (för att betjäningslagret) och hanterar endast senaste data.
 5. Alla frågor besvaras genom att sammanfoga resultaten från batch-vyer och i realtid vyer.

### <a name="resources"></a>Resurser

* **Nya data**: Den [stream från Twitter-flöde till CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), vilket är mekanismen för att skicka nya data till Azure Cosmos DB.
* **Batchlager:** Batch-lagret består av den *master datauppsättning* (en oföränderlig, Lägg endast uppsättning rådata) och möjligheten att beräkna före batch vyer av data som skickas till den **betjäningslagret**.
   * Den **Lambda-arkitektur Rearchitected - Batchlager** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) frågor i *master datauppsättning* uppsättning vyer för batch.
* **Betjäningslagret:** Den **betjäningslagret** består av förberäknade data, vilket resulterar i batch-vyer (till exempel aggregeringar, specifika utsnitt osv.) för snabba frågor.
  * Den **Lambda-arkitektur Rearchitected - Batch betjänar lagret** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) skickar batchdata till betjäningslagret, det vill säga Spark frågar en batch samling tweets, bearbetar dessa och lagrar den i en annan samling (en beräknad batch).
    * **Hastighetslagret:** Den **hastighetslagret** består av Spark med Azure Cosmos DB-ändringsflödet för att läsa och agera på omedelbart. Data kan också att sparas i *beräknad RT* så att andra system kan fråga de bearbetade realtidsdata till skillnad från kör en i realtid fråga själva.
  * Den [Streaming frågan från Cosmos-DB ändra flöde](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala skriptet kör en strömmande fråga från Azure Cosmos DB-ändringsflödet för att beräkna ett intervall för antal från spark-shell.
  * Den [Streaming taggar frågan från Cosmos-DB ändra flöde](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala skriptet kör en strömmande fråga från Azure Cosmos DB-ändringsflödet för att beräkna ett intervall för antal taggar från spark-shell.
  
## <a name="next-steps"></a>Nästa steg
Om du inte redan gjort hämta Spark till Azure Cosmos DB-anslutningen från den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-lagringsplatsen och utforska ytterligare resurser i lagringsplatsen:
* [Lambda-arkitektur](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Distribuerade aggregeringar exempel](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Exempel på skript och anteckningsböcker](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Strukturerad direktuppspelning demonstrationer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Demonstrationer för ändringsfeed](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Stream bearbeta ändringar med hjälp av Azure Cosmos DB Change Feed och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Du kan också gå igenom den [Apache Spark SQL och DataFrames datauppsättningar Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) och [Apache Spark på Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artikeln.
