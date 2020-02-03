---
title: Lambda-arkitektur med Azure Cosmos DB och Apache Spark
description: Den här artikeln beskriver hur du implementerar en lambda-arkitektur med Azure Cosmos DB och HDInsight Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719746"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementera en lambda-arkitektur på Azure-plattformen 

Lambda-arkitekturer aktivera effektiv databearbetning massiva datauppsättningar. Lambda-arkitektur använda batch-bearbetning, strömbearbetning och en betjäningslagret för att minimera fördröjning som ingår i fråga stordata. 

Du kan kombinera följande tekniker för att påskynda analyser av stordata i realtid för att implementera en lambda-arkitektur på Azure:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), branschens första distribuerade databas tjänst för flera modeller. 
* [Apache Spark för Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), ett bearbetnings ramverk som kör storskaliga data analys program
* Azure Cosmos DB [ändra feed](change-feed.md), som strömmar nya data till batch-lagret för att HDInsight ska kunna bearbeta
* [Spark till Azure Cosmos DB-anslutning](spark-connector.md)

Den här artikeln beskriver grunderna i en lambda-arkitekturen baserat på den ursprungliga designen i flera lager och fördelarna med en ”rearchitected” lambda-arkitektur som förenklar åtgärder.  

## <a name="what-is-a-lambda-architecture"></a>Vad är en lambda-arkitekturen?
En lambda-arkitektur är en generisk, skalbar och feltolerant data bearbetnings arkitektur för att hantera scenarier för batch-och hastighets svars tider enligt beskrivningen i [Nathan Marz](https://twitter.com/nathanmarz).

![Diagram som visar en lambda-arkitektur](./media/lambda-architecture/lambda-architecture-intro.png)

Källa: http://lambda-architecture.net/

De grundläggande principerna för en lambda-arkitektur beskrivs i föregående diagram som per [http://lambda-architecture.net](http://lambda-architecture.net/).

 1. Alla **data** överförs till *både* *batch* -skiktet och *hastighets skiktet*.
 2. **Batch-lagret** har en huvud data uppsättning (oföränderligt, endast tillagda data uppsättningar) och för att beräkna batch-vyerna.
 3. **Betjänar lagret** har batch-vyer för snabba frågor. 
 4. **Hastighets lagret** kompenserar för bearbetnings tiden (till betjänande lager) och hanterar endast senaste data.
 5. Alla frågor besvaras genom att koppla samman resultat från batch-vyer och i realtid vyer eller pinga dem individuellt.

Vid läsning av ytterligare, kommer vi att kunna genomföra den här arkitekturen använder endast följande:

* Azure Cosmos-behållare
* Kluster för HDInsight (Apache Spark 2.1)
* Spark-koppling [1,0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Hastighetslagret

En ur åtgärder kan det vara en komplicerad åtagande att upprätthålla två dataströmmar som säkerställer att rätt tillstånd för data. För att förenkla driften kan du använda [Azure Cosmos DB ändra feed-stöd](change-feed.md) för att behålla statusen för *batch-lagret* , samtidigt som du avslöjar Azure Cosmos DB ändrings loggen via *API: et för byte av byte* för ditt *hastighets skikt*.  
![diagram som markerar det nya data, hastighets lagret och den överordnade huvud data uppsättningen i lambda-arkitekturen](./media/lambda-architecture/lambda-architecture-change-feed.png)

Vad är viktigt i dessa lager:

 1. Alla **data** överförs *endast* till Azure Cosmos DB, vilket innebär att du kan undvika problem med flera data.
 2. **Batch-lagret** har en huvud data uppsättning (oföränderligt, endast tillagda data uppsättningar) och för att beräkna batch-vyerna.
 3. **Bebetjänings lagret** diskuteras i nästa avsnitt.
 4. **Hastighets lagret** använder HDInsight (Apache Spark) för att läsa Azure Cosmos DB ändra feed. På så sätt kan du spara filer samt att fråga och bearbeta dem samtidigt.
 5. Alla frågor besvaras genom att koppla samman resultat från batch-vyer och i realtid vyer eller pinga dem individuellt.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Kodexempel: Spark structured streaming till ett Azure Cosmos DB-ändringsflödet
Om du vill köra en snabb prototyp av Azure Cosmos DB ändra feed som en del av **hastighets skiktet**, kan du testa det med Twitter-data som en del av [data strömmens bearbetnings ändringar med Azure Cosmos DB ändra feed och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) exempel. För att komma igång med dina Twitter-utdata, se kod exemplet i [Stream-flödet från Twitter till Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). Du läser in Twitter-data till Azure Cosmos DB och du kan ställa in ditt kluster i HDInsight (Apache Spark) att ansluta till den ändringsflödet med föregående exempel. Mer information om hur du konfigurerar den här konfigurationen finns i [Apache Spark för att Azure Cosmos DB anslutnings installationen](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Följande kodfragment visar hur du konfigurerar `spark-shell` att köra ett strukturerat strömmande jobb för att ansluta till en Azure Cosmos DB ändra feed, som granskar Twitter-dataströmmen i real tid för att utföra ett antal körnings intervall.

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

Fullständiga kod exempel finns i [Azure-cosmosdb-Spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), inklusive:
* [Strömmande fråga från Cosmos DB ändra feed. Scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Direkt uppspelning av Taggar fråga från Cosmos DB ändra feed. Scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Resultatet av detta är en `spark-shell`-konsol, som kontinuerligt kör ett strukturerat strömmande jobb som utför ett intervall med data från Twitter-data från Azure Cosmos DB ändra feed. Följande bild visar utdata från stream-jobbet och intervallet räknas.

![Utdata visar intervallantalet mot Twitter-data från Azure Cosmos DB-ändringsflödet för direktuppspelning](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Mer information om Azure Cosmos DB-ändringsflödet, se:

* [Arbeta med stöd för ändrings flöden i Azure Cosmos DB](change-feed.md)
* [Introduktion till Azure CosmosDB Change feed processor-biblioteket](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Ändringar i ström bearbetning: Azure CosmosDB Change feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch- och betjänar lager
Eftersom nya data läses in i Azure Cosmos DB (där ändrings flödet används för hastighets skiktet), är det här där **huvud data uppsättningen** (en oföränderlig, tilläggs uppsättning rå data) finns. Från och med kan du använda HDInsight (Apache Spark) för att utföra förberäknings funktioner från **batch-lagret** för att **betjäna lager**, som du ser i följande bild:

![Diagram som visar batchlager och betjäningslagret med lambda-arkitekturen](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Vad är viktigt i dessa lager:

 1. Alla **data** överförs endast till Azure Cosmos dB (för att undvika problem med flera sändningar).
 2. **Batch-lagret** har en huvud data uppsättning (oföränderlig, endast tillagd uppsättning rå data) som lagras i Azure Cosmos dB. Med HDI Spark kan beräkna du förväg din aggregeringar lagras i dina beräknade batch-vyer.
 3. **Betjänande skikt** är en Azure Cosmos-databas med samlingar för huvud data uppsättningen och den beräknade vyn.
 4. **Hastighets lagret** diskuteras senare i den här artikeln.
 5. Alla frågor besvaras genom att sammanfoga resultatet från batch-vyer i realtid vyer, pinga dem individuellt.

### <a name="code-example-pre-computing-batch-views"></a>Kodexempel: före databehandling batch vyer
Om du vill visa hur du kör förberäknade vyer mot din **huvud data uppsättning** från Apache Spark till Azure Cosmos DB använder du följande kodfragment från antecknings bokens [lambda-arkitektur med hjälp av batch-lager](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) och [återskapande av lambda-arkitektur för att betjäna lager](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). I det här scenariot använder du Twitter-data som lagras i Azure Cosmos DB.

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

Nu när du har din fråga kan vi spara tillbaka dem till en samling med hjälp av Spark-anslutningen för att spara utdata till en annan samling.  I det här exemplet använder du Scala för att demonstrera anslutningen. Precis som i föregående exempel skapar du konfigurations anslutningen för att spara Apache Spark DataFrame till en annan Azure Cosmos-behållare.

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

När du har angett `SaveMode` (som anger om du vill `Overwrite` eller `Append` dokument) skapar du en `tweets_bytags`-DataFrame som liknar Spark SQL-frågan i föregående exempel.  När `tweets_bytags` DataFrame har skapats kan du spara den med hjälp av metoden `write` med hjälp av den tidigare angivna `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Den här sista instruktionen har nu sparat din spark-DataFrame i en ny Azure Cosmos-behållare. från ett lambda-arkitektur perspektiv är detta din **batch-vy** i **betjänar skiktet**.
 
#### <a name="resources"></a>Resurser

Fullständiga kod exempel finns i [Azure-cosmosdb-Spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) , inklusive:
* Lambda-arkitektur rekonstruerad-batch Layer [HTML-](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda-arkitektur rekonstruerad – batch för att betjäna lager [-HTML-](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Hastighetslagret
Som tidigare meddelanden, som använder Azure Cosmos DB ändringen Feed-biblioteket kan du förenkla åtgärder mellan batch och hastighet lager. I den här arkitekturen använder du Apache Spark (via HDInsight) för att utföra *strukturerade strömnings* frågor mot data. Du kanske också vill temporärt spara resultatet av dina strukturerad direktuppspelning frågor så att andra system kan komma åt dessa data.

![Schemat betonar hastighetslagret av lambda-arkitekturen](./media/lambda-architecture/lambda-architecture-speed.png)

Det gör du genom att skapa en separat Azure Cosmos-behållare för att spara resultatet av dina strukturerade strömnings frågor.  Detta gör att du kan ha andra system åtkomst den här informationen inte bara Apache Spark. Du kan också konfigurera dina dokument som ska tas bort automatiskt efter en angiven varaktighet med funktionen Time-to-Live (TTL) Cosmos DB.  Mer information om Azure Cosmos DB TTL-funktionen finns i [förfaller data i Azure Cosmos-behållare automatiskt med Time to Live](time-to-live.md)

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

* **Nya data**: [Stream-flödet från Twitter till CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), vilket är mekanismen för att skicka nya data till Azure Cosmos dB.
* **Batch-skikt:** Batch-lagret består av *huvud data uppsättningen* (en skrivskyddad uppsättning med rå data) och möjligheten att i förväg beräkna batch-vyer för de data som skickas till **bebetjänings skiktet**.
   * **Lambda-arkitekturen som återskapas – batch-** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) frågar *huvud data* uppsättningens uppsättning batch-vyer.
* **Betjänar lager:** Beställnings **skiktet** består av förberäknade data som skapas i batch-vyer (till exempel agg regeringar, speciella utsnitt osv.) för snabba frågor.
  * **Lambda-arkitekturen rekonstruerad – batch för att betjäna** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) i lager Notebook | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) pushar batch-data till betjänar skiktet. det vill säga Spark frågar en batch-samling av tweets, bearbetar den och lagrar den i en annan samling (en beräknad batch).
    * **Hastighets skikt:** **Hastighets lagret** består av Spark som använder Azure Cosmos DB ändra feed för att läsa och agera direkt. Data kan också sparas i *beräknade RT* så att andra system kan fråga de bearbetade real tids data i stället för att köra real tids frågor själva.
  * [Direkt uppspelnings frågan från Cosmos DB Change feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) Scala-skriptet kör en strömmande fråga från Azure Cosmos DB ändra feed för att beräkna ett intervall antal från Spark-Shell.
  * De [strömmande taggarna fråga från Cosmos DB Change feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) Scala-skriptet kör en strömmande fråga från Azure Cosmos DB ändra feed för att beräkna ett intervall av taggar från Spark-Shell.
  
## <a name="next-steps"></a>Nästa steg
Om du inte redan har gjort det kan du hämta Spark till Azure Cosmos DB Connector från [Azure-cosmosdb-Spark GitHub-](https://github.com/Azure/azure-cosmosdb-spark) lagringsplatsen och utforska de ytterligare resurserna i lagrings platsen:
* [Lambda-arkitektur](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Exempel på distribuerade agg regeringar](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Exempel på skript och antecknings böcker](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Självstrukturerade strömnings demonstrationer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Ändra feed-demonstrationer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Bearbetning av ström bearbetnings ändringar med Azure Cosmos DB ändra feed och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Du kanske också vill läsa [guiden Apache Spark SQL, DataFrames och data uppsättningar](https://spark.apache.org/docs/latest/sql-programming-guide.html) och [Apache Spark på Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) .
