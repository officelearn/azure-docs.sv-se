---
title: Lambda-arkitektur med Azure Cosmos DB och Apache Spark
description: I den här artikeln beskrivs hur du implementerar en lambda-arkitektur med Azure Cosmos DB, HDInsight och Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76719746"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementera en lambda-arkitektur på Azure-plattformen 

Lambda-arkitekturer möjliggör effektiv databehandling av massiva datamängder. Lambda-arkitekturer använder batchbearbetning, dataflödesbearbetning och ett serveringslager för att minimera svarstiden som är involverad i att fråga stordata. 

Om du vill implementera en lambda-arkitektur på Azure kan du kombinera följande tekniker för att påskynda stordataanalys i realtid:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), branschens första globalt distribuerade databastjänst med flera modeller. 
* [Apache Spark för Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), ett bearbetningsramverk som kör storskaliga dataanalysprogram
* Azure Cosmos DB [ändringsfeed](change-feed.md), som strömmar nya data till batchlagret för HDInsight att bearbeta
* [Spark till Azure Cosmos DB Connector](spark-connector.md)

Denna artikel beskriver grunderna i en lambda arkitektur baserad på den ursprungliga flera lager design och fördelarna med en "rearchitected" lambda arkitektur som förenklar verksamheten.  

## <a name="what-is-a-lambda-architecture"></a>Vad är en lambda arkitektur?
En lambda-arkitektur är en generisk, skalbar och feltolerant databehandlingsarkitektur för att hantera batch- och hastighetsfördröjningsscenarier enligt beskrivningen av [Nathan Marz](https://twitter.com/nathanmarz).

![Diagram som visar en lambda-arkitektur](./media/lambda-architecture/lambda-architecture-intro.png)

Källa: http://lambda-architecture.net/

De grundläggande principerna för en lambda arkitektur beskrivs [http://lambda-architecture.net](http://lambda-architecture.net/)i föregående diagram enligt .

 1. Alla **data** trycks in i *både* *batchlagret* och *hastighetslagret*.
 2. **Batchlagret** har en huvuddatauppsättning (oföränderlig, tilläggsuppsättning med rådata) och förberäknar batchvyerna.
 3. **Serveringslagret** har batchvyer för snabba frågor. 
 4. **Hastighetslagret** kompenserar för bearbetningstiden (till serveringslagret) och behandlar endast de senaste uppgifterna.
 5. Alla frågor kan besvaras genom att slå samman resultat från batchvyer och realtidsvyer eller pinga dem individuellt.

Vid ytterligare läsning kommer vi att kunna implementera den här arkitekturen med endast följande:

* Azure Cosmos-behållare
* HDInsight -kluster (Apache Spark 2.1)
* Tändstift [1,0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Hastighet lager

Ur ett verksamhetsperspektiv kan det vara komplicerat att underhålla två dataströmmar samtidigt som du säkerställer att data är korrekta. För att förenkla åtgärder, använd [Azure Cosmos DB ändra foder stöd](change-feed.md) för att hålla tillståndet för *batch-lagret* samtidigt som Azure Cosmos DB ändringslogg via *Ändra Feed API* för din hastighet *lager*.  
![Diagram som visar nya data, hastighetslager och huvuddatauppsättningsdel av lambda-arkitekturen](./media/lambda-architecture/lambda-architecture-change-feed.png)

Vad är viktigt i dessa lager:

 1. Alla **data** överförs *endast* till Azure Cosmos DB, vilket innebär att du kan undvika problem med flera castningar.
 2. **Batchlagret** har en huvuddatauppsättning (oföränderlig, tilläggsuppsättning med rådata) och förberäknar batchvyerna.
 3. **Serveringslagret** diskuteras i nästa avsnitt.
 4. **Hastighetslagret** använder HDInsight (Apache Spark) för att läsa Azure Cosmos DB-ändringsflödet. På så sätt kan du spara dina data samt fråga och bearbeta dem samtidigt.
 5. Alla frågor kan besvaras genom att slå samman resultat från batchvyer och realtidsvyer eller pinga dem individuellt.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Kodexempel: Utlösa strukturerad direktuppspelning till en Azure Cosmos DB-ändringsfeed
Om du vill köra en snabbprototyp av Azure Cosmos DB-ändringsflödet som en del av **hastighetslagret**kan du testa den med Twitter-data som en del av [dataströmbearbetningsändringarna med azure cosmos DB Change Feed och Apache](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) Spark-exemplet. Om du vill starta twitterutdatan läser du kodexemplet i [flödet Stream från Twitter till Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). Med föregående exempel läser du in Twitter-data i Azure Cosmos DB och du kan sedan konfigurera ditt APACHE Spark-kluster (Apache Spark) för att ansluta till ändringsflödet. Mer information om hur du konfigurerar den här konfigurationen finns i [Apache Spark till Azure Cosmos DB Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Följande kodavsnitt visar hur du `spark-shell` konfigurerar för att köra ett strukturerat direktuppspelningsjobb för att ansluta till en Azure Cosmos DB-ändringsfeed, som granskar realtidsdataströmmen för Twitter, för att utföra ett antal intervaller.

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

Fullständiga kodexempel finns i [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), inklusive:
* [Strömmande fråga från Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Strömmande taggar fråga från Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Utdata för detta `spark-shell` är en konsol som kontinuerligt kör ett strukturerat direktuppspelningsjobb som utför ett intervallantal mot Twitter-data från Azure Cosmos DB-ändringsflödet. Följande bild visar utdata för strömjobbet och intervallantalet.

![Strömmande utdata som visar intervallantalet mot Twitter-data från Azure Cosmos DB-ändringsflödet](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Mer information om Azure Cosmos DB-ändringsflöde finns i:

* [Arbeta med stöd för ändringsfeed i Azure Cosmos DB](change-feed.md)
* [Vi presenterar Azure CosmosDB-processorbiblioteket för ändringsfeed](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Ändra dataströmbearbetning: Azure CosmosDB-ändringsflöde + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch- och serveringslager
Eftersom de nya data läses in i Azure Cosmos DB (där ändringsflödet används för hastighetslagret), är det här **huvuddatauppsättningen** (en oföränderlig, tilläggsuppsättning med rådata) finns. Från denna punkt och framåt, använd HDInsight (Apache Spark) för att utföra förberäkningsfunktioner från **batchlagret** till **serveringslager**, som visas i följande bild:

![Diagram som belyser batchlagret och serveringsskiktet i lambdaarkitekturen](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Vad är viktigt i dessa lager:

 1. Alla **data** överförs endast till Azure Cosmos DB (för att undvika problem med flera cast).
 2. **Batchlagret** har en huvuddatauppsättning (oföränderlig, tilläggsuppsättning med rådata) som lagras i Azure Cosmos DB. Med HDI Spark kan du förberäkna dina aggregeringar som ska lagras i dina beräknade batchvyer.
 3. **Serveringslagret** är en Azure Cosmos-databas med samlingar för huvuddatauppsättningen och beräknad batchvy.
 4. **Hastighetslagret** diskuteras senare i den här artikeln.
 5. Alla frågor kan besvaras genom att slå samman resultat från batchvyer och realtidsvyer, eller pinga dem individuellt.

### <a name="code-example-pre-computing-batch-views"></a>Kodexempel: Batchvyer före datoranvändning
Om du vill visa hur du kör förberäknade vyer mot **huvuddatauppsättningen** från Apache Spark till Azure Cosmos DB använder du följande kodavsnitt från de anteckningsböcker [Lambda Architecture Rearchitected - Batch Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) och [Lambda Architecture Rearchitected - Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). I det här fallet använder Twitter-data som lagras i Azure Cosmos DB.

Låt oss börja med att skapa konfigurationsanslutningen till Twitter-data i Azure Cosmos DB med PySpark-koden nedan.

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

Låt oss sedan köra följande Spark SQL-uttryck för att bestämma de 10 hashtaggarna i uppsättningen tweets. För den här Spark SQL-frågan kör vi detta i en Jupyter-anteckningsbok utan utdatastapeldiagrammet direkt efter det här kodavsnittet.

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

![Diagram som visar antalet tweets per hashtagg](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Nu när du har din fråga, låt oss spara tillbaka den till en samling med hjälp av Spark Connector för att spara utdata i en annan samling.  I det här exemplet använder du Scala för att visa upp anslutningen. I likhet med föregående exempel skapar du konfigurationsanslutningen för att spara Apache Spark DataFrame i en annan Azure Cosmos-behållare.

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

När du `SaveMode` har angett `Overwrite` (ange om eller `Append` dokument) skapar du en `tweets_bytags` DataFrame som liknar Spark SQL-frågan i föregående exempel.  Med `tweets_bytags` DataFrame skapad kan du `write` spara den med `writeConfig`den metod som tidigare angivets .

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Den här sista satsen har nu sparat spark-dataramen i en ny Azure Cosmos-behållare. från en lambda arkitektur perspektiv, detta är din **batch vy** inom **serveringsskiktet**.
 
#### <a name="resources"></a>Resurser

Fullständiga kodexempel finns i [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) inklusive:
* Lambda Arkitektur Bakåtriktad - Batch Layer [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda Arkitektur Rearchitected - Batch till Servering Layer [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Hastighet lager
Som tidigare nämnts kan du med hjälp av Azure Cosmos DB Change Feed Library förenkla åtgärderna mellan batch- och hastighetslagren. I den här arkitekturen använder du Apache Spark (via HDInsight) för att utföra *strukturerade direktuppspelningsfrågor* mot data. Du kanske också vill spara resultaten av dina strukturerade direktuppspelningsfrågor tillfälligt så att andra system kan komma åt dessa data.

![Diagram som belyser speed layer av lambda arkitektur](./media/lambda-architecture/lambda-architecture-speed.png)

Det gör du genom att skapa en separat Azure Cosmos-behållare för att spara resultaten av dina strukturerade strömningsfrågor.  Detta gör att du kan ha andra system tillgång till denna information inte bara Apache Spark. Förutom med funktionen Cosmos DB Time-to-Live (TTL) kan du konfigurera dina dokument så att de tas bort automatiskt efter en viss varaktighet.  Mer information om Azure Cosmos DB TTL-funktionen finns [i Förfalla data i Azure Cosmos-behållare automatiskt med tid att leva](time-to-live.md)

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

## <a name="lambda-architecture-rearchitected"></a>Lambda arkitektur: Rearchitected
Som anges i föregående avsnitt kan du förenkla den ursprungliga lambda-arkitekturen med hjälp av följande komponenter:
* Azure Cosmos DB
* Azure Cosmos DB Change Feed Library för att undvika behovet av att flergjuta dina data mellan batch- och hastighetslagren
* Apache Spark på HDInsight
* Spark Connector för Azure Cosmos DB

![Diagram som visar lambda-arkitekturens bakåtriktning med Azure Cosmos DB, Spark och Azure Cosmos DB Change Feed API](./media/lambda-architecture/lambda-architecture-re-architected.png)

Med den här designen behöver du bara två hanterade tjänster, Azure Cosmos DB och HDInsight. Tillsammans tar de itu med partiet, servering och hastighet lager av lambda arkitektur. Detta förenklar inte bara åtgärderna utan även dataflödet. 
 1. Alla data överförs till Azure Cosmos DB för bearbetning
 2. Batchlagret har en huvuddatauppsättning (oföränderlig, tilläggsuppsättning med rådata) och förberäknar batchvyerna
 3. Serveringslagret har batchvyer med data för snabba frågor.
 4. Hastighetslagret kompenserar för bearbetningstiden (till serveringslagret) och behandlar endast de senaste uppgifterna.
 5. Alla frågor kan besvaras genom att slå samman resultat från batchvyer och realtidsvyer.

### <a name="resources"></a>Resurser

* **Nya data**: [Flödet foder från Twitter till CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), som är mekanismen för att driva nya data till Azure Cosmos DB.
* **Batch-lager:** Batchlagret består av *huvuddatauppsättningen* (en oföränderlig, tilläggsbar uppsättning rådata) och möjligheten att förbeställa batchvyer av data som skjuts in i **serveringslagret**.
   * **Den Lambda Architecture Rearchitected - Batch Layer** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) frågar *huvuddatauppsättningen* av batchvyer.
* **Serveringslager:** **Serveringslagret** består av fördat beräknade data som resulterar i batchvyer (till exempel aggregeringar, specifika utsnitt osv.) för snabba frågor.
  * **Lambda Architecture Rearchitected - Batch to Serving Layer** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) skickar batchdata till serveringslagret; Det vill än, Spark frågar en batch samling tweets, bearbetar den och lagrar den i en annan samling (en beräknad batch).
    * **Hastighetsskikt:** **Hastighetslagret** består av Spark som använder Azure Cosmos DB-ändringsflödet för att läsa och agera direkt. Data kan också sparas till *beräknad RT* så att andra system kan fråga bearbetade realtidsdata i stället för att köra en realtidsfråga själva.
  * [Strömningsfrågan från Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala-skript kör en strömningsfråga från Azure Cosmos DB-ändringsflödet för att beräkna ett intervallantal från spark-shell.
  * [Den strömmande taggar frågan från Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala skript kör en strömmande fråga från Azure Cosmos DB ändringsfeed för att beräkna ett intervallantal taggar från spark-shell.
  
## <a name="next-steps"></a>Nästa steg
Om du inte redan har gjort det kan du hämta Spark till Azure Cosmos [DB-anslutningen från Azure-cosmosdb-spark GitHub-databasen](https://github.com/Azure/azure-cosmosdb-spark) och utforska ytterligare resurser i repo:
* [Lambda-arkitektur](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Exempel på distribuerade aggregeringar](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Exempel på skript och anteckningsböcker](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Strukturerade strömmande demos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Ändra feeddemoer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Strömma bearbetningsändringar med Azure Cosmos DB Change Feed och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Du kanske också vill granska [Apache Spark SQL, DataFrames och Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) och Apache Spark på Azure [HDInsight-artikeln.](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
