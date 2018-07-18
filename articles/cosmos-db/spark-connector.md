---
title: Apache Spark att ansluta till Azure Cosmos DB | Microsoft Docs
description: Använd den här självstudiekursen vill veta mer om Azure Cosmos DB Spark-anslutningsappen som gör det möjligt att ansluta Apache Spark i Azure Cosmos DB för att utföra distribuerade aggregeringar och data sciences på flera innehavare globalt distribuerat databassystem från Microsoft som är utformad för molnet.
keywords: Apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: cae66a40882231f7762af29cdeaaf658dd2aa968
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113955"
---
# <a name="accelerate-real-time-big-data-analytics-by-using-the-spark-to-azure-cosmos-db-connector"></a>Påskynda analys i realtid big data med hjälp av Spark kan Azure Cosmos DB-anslutningsapp
 
Spark kan Azure Cosmos DB-anslutningsapp kan Azure Cosmos DB för att fungera som indata eller utdata för Apache Spark-jobb. Ansluta [Spark](http://spark.apache.org/) till [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ökar dina möjligheter att lösa snabbrörliga data science problem där du kan använda Azure Cosmos DB för att snabbt spara och fråga efter data. Spark kan Azure Cosmos DB-anslutningen använder effektivt de inbyggda Azure Cosmos DB-hanterade indexen. Index kan använda uppdateringsbara kolumner när du utföra analyser och push-down-filtrering med predikat mot föränderliga globalt distribuerade data, vilket kan vara allt från Internet of Things (IoT) scenarier för datavetenskap och analys av data.

Läs mer om Spark kan Azure Cosmos DB-anslutningen i den här videon:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

## <a name="connector-components"></a>Connector-komponenter

Spark kan Azure Cosmos DB-anslutningen använder följande komponenter:

* [Azure Cosmos DB](http://documentdb.com) ger kunder möjlighet att etablera och Elastiskt skala både dataflöde och lagring över valfritt antal geografiska regioner.  

* [Apache Spark](http://spark.apache.org/) är en motor för händelsebearbetning kraftfulla öppen källkod som är uppbyggd kring hastighet, enkel användning och avancerade analyser.  

* [Apache Spark-kluster på Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) att köra spark-jobb i spark-klustret.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Apache Spark ansluta till Azure Cosmos DB

Det finns två sätt att ansluta Apache Spark och Azure Cosmos DB:

1. Med hjälp av [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python), en Python-baserade spark kan Cosmos DB-anslutningstjänsten, som även kallas ”pyDocumentDB”.  

2. Med hjälp av den [Java SDK för Azure Cosmos DB SQL](https://github.com/Azure/azure-documentdb-java) en Java-baserad spark till Cosmos DB-anslutningstjänsten.


**Versioner som stöds**

| Komponent | Version |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Databricks-körningsversion | > 3.4 |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Ansluta med hjälp av Python eller pyDocumentDB SDK

Följande bild illustrerar arkitekturen i pyDocumentDB SDK implementering:

![Spark på Azure Cosmos DB dataflöde via pyDocumentDB DB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Dataflöde

Dataflöde för implementeringen av pyDocumentDB är följande:

* Huvudnoden spark ansluter till Azure Cosmos DB gateway-noden via pyDocumentDB. En användare anger spark och Azure Cosmos DB-anslutningar. Anslutningar till respektive huvud- och gateway-noderna är transparent för användaren.  

* Gateway-noden gör frågan mot Azure Cosmos DB där frågan därefter körs mot samlingens partitioner i datanoderna. Svaret för dessa frågor skickas tillbaka till gateway-noden och den resultatuppsättningen returneras till spark-huvudnoden.  

* Efterföljande frågor (till exempel mot spark data frame) skickas till arbetsnoder Spark för bearbetning.  

Kommunikation mellan spark och Azure Cosmos DB är begränsad till spark-huvudnod och Azure Cosmos DB gateway-noder. Frågorna gå så snabbt som gör att transportlagret mellan dessa två noder.

Kör du följande steg för att ansluta spark till Azure Cosmos DB med hjälp av pyDocumentDB SDK:

1. Skapa en [Azure Databricks-arbetsytan](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) och en [spark-kluster](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (Databricks körningsversion 4.0 (inkluderar Apache Spark 2.3.0, Scala 2.11) i den arbetsytan.  

2. När klustret har skapats och körs, går du till **arbetsytan** > **skapa** > **biblioteket**.  
3. Dialogrutan Nytt bibliotek väljer **ladda upp Python ägg eller PyPi** som källa, ange **pydocumentdb** som namn och välj **installera biblioteket**. PyDocumentdb SDK har redan publicerats till pip-paketen så att du kan hitta den och installera. 

   ![Skapa och bifoga bibliotek](./media/spark-connector/create-library.png)

4. När biblioteket har installerats kan du koppla den till klustret du skapade tidigare.  

5. Gå sedan till den **arbetsytan** > **skapa** > **Notebook**.  

6. I den **Skapa anteckningsbok** dialogrutan rutan, ange ett användarvänligt namn, Välj **Python** som språk. Välj det kluster som du skapade tidigare i listrutan och välj **skapa**.  

7. Enkelheten med kommunikation transport gör körning av en fråga från spark till Azure Cosmos DB med hjälp av pyDocumentDB relativt enkla. Därefter kör du några frågor i spark med hjälp av exempeldata flyg finns i ”doctorwho” Cosmos DB-konto som är allmänt tillgänglig. HTML-versionen av den bärbara datorn är värd för den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-lagringsplatsen. Hämta databasfiler och navigera till `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html` du kan importera anteckningsboken till ditt Azure Databricks-konto och kör den. I följande avsnitt beskrivs funktioner i kodblock i detaljerad.

Följande kodfragment visar hur du importerar pyDocumentDB SDK och köra en fråga i spark-kontext. Enligt vad som anges i kodfragmentet innehåller pyDocumentDB SDK anslutningsparametrar som krävs för att ansluta till Azure Cosmos DB-konto. Det importerar bibliotek som krävs, konfigurerar huvudnyckel och värden för att skapa Azure Cosmos DB-klienten (pydocumentdb.document_client).


```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]

# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)

```

Därefter kan du köra frågor, kodfragment ansluter till samlingen airports.codes i DoctorWho-konto och kör en fråga för att extrahera flygplats städer i delstaten Washington. 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

När frågan körs är resultatet en ”query_iterable. QueryIterable ”som konverteras till en Python-lista, som sedan konverteras till spark data frame. 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

## <a name="considerations-when-using-pydocumentdb-sdk"></a>Att tänka på när du använder pyDocumentDB SDK
Ansluta spark till Azure Cosmos DB med hjälp av pyDocumentDB SDK rekommenderas i följande scenarier:

* Du vill använda Python.  

* Du returnerar ett relativt litet resultat från Azure Cosmos DB på spark. Observera att den underliggande datauppsättningen i Azure Cosmos DB kan vara ganska stor och du tillämpa filter eller kör predikat filter mot Azure Cosmos DB-källa.

## <a name="connect-by-using-the-java-sdk"></a>Ansluta med hjälp av Java SDK

Följande bild illustrerar arkitekturen i Azure Cosmos DB SQL Java SDK-implementering och flyttas data mellan arbetsnoder spark:

![Dataflödet i Spark för Azure Cosmos DB-anslutningsapp](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Dataflöde

Dataflöde för Java SDK-implementeringen är följande:

* Spark-huvudnoden ansluter till Azure Cosmos DB gateway-noden att hämta partition kartan. En användare anger endast spark och Azure Cosmos DB-anslutningar. Anslutningar till respektive huvud- och gateway-noderna är transparent för användaren.  

* Den här informationen tillhandahålls tillbaka till spark-huvudnoden. Du bör nu kunna parsa frågan för att fastställa partitionerna och deras platser i Azure Cosmos DB som du behöver komma åt.  

* Den här informationen skickas till arbetsnoder spark.  

* Spark-arbetsnoder ansluta till Azure Cosmos DB-partitioner direkt att extrahera data och returnera data till spark-partitioner i arbetsnoderna.  

Kommunikation mellan spark och Azure Cosmos DB är betydligt snabbare på grund av data är att flytta mellan arbetsnoder spark och Azure Cosmos DB-datanoder (partitioner). I detta dokument kan du skicka twitter-exempeldata till Azure Cosmos DB-konto och köra spark-frågor med hjälp av dessa data. Använd följande steg för att skriva exempel Twitter-data till Azure Cosmos DB:

1. Skapa en [Azure Cosmos DB SQL API-konto](create-sql-api-dotnet.md#create-a-database-account) och [Lägg till en samling](create-sql-api-dotnet.md#add-a-collection) till kontot.  

2. Ladda ned den [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) exemplet från GitHub, som används för att skriva exempel Twitter-data till Azure Cosmos DB.  

3. Öppna en kommandotolk och installera Tweepy och pyDocumentdb moduler genom att köra följande kommandon:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Extrahera innehållet i Twitter-feed exemplet och öppna filen config.py. Uppdatera masterKey, värden, databaseId, collectionId och preferredLocations värden.  

5. Gå till `http://apps.twitter.com/` och registrera den Twitter-flöde skriptet som ett nytt program. När du har valt ett namn och program för din app, visas med en **använda nyckeln, konsumenthemligheten, åtkomst-token och åtkomsttokenhemligheten**. Kopiera dessa värden och uppdatera dem i config.py filen att ge programmet programmässig åtkomst till Twitter.   

6. Spara filen config.py. Öppna Kommandotolken och kör python-program med hjälp av följande kommando:

   ```bash
   Python driver.py
   ```

7. Gå till Azure Cosmos DB-samling i portalen och kontrollera att twitter-data skrivs till samlingen.

### <a name="find-and-attach-java-sdk-to-the-spark-cluster"></a>Hitta och ansluta Java SDK till spark-kluster

1. Skapa en [Azure Databricks-arbetsytan](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) och en [spark-kluster](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (Databricks körningsversion 4.0 (inkluderar Apache Spark 2.3.0, Scala 2.11) i den arbetsytan.  

2. När klustret har skapats och körs, går du till **arbetsytan** > **skapa** > **biblioteket**.  

3. Dialogrutan Nytt bibliotek väljer **Maven-koordinat** som källa, anger du koordinaten värde **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**, och välj  **Skapa bibliotek**. Maven-beroenden har lösts och paketet har lagts till i din arbetsyta. I ovanstående maven koordinaten format, 2.3.0 representerar spark-version och 2.11 representerar scala-version 1.2.0 representerar den Azure Cosmos DB connector-versionen. 

4. När biblioteket har installerats kan du koppla den till klustret du skapade tidigare. 

Den här artikeln visar hur du använder spark-anslutningsappen Java SDK i följande scenarier:

* Läsa twitter-data från Azure Cosmos DB  

* Läsa twitter-data som strömmar till Azure Cosmos DB  

* Skriva twitter-data till Azure Cosmos DB 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Läsa twitter-data från Azure Cosmos DB
 
I det här avsnittet ska köra du spark-frågor för att läsa en batch med Twitter-data från Azure Cosmos DB. HTML-versionen av den bärbara datorn är värd för den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-lagringsplatsen. Hämta databasfiler och navigera till `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html` importera anteckningsboken till ditt Azure Databricks-konto, uppdatera kontot URI, huvudnyckel, databas, samlingsnamn och köra den eller skapa anteckningsboken på följande sätt:

1. Navigera till ditt Azure Databricks-konto och välj den **arbetsytan** > **skapa** > **Notebook**. 

2. I den **Skapa anteckningsbok** dialogrutan rutan, ange ett användarvänligt namn, Välj **Python** som språk, i listrutan väljer du klustret som du skapade tidigare och välj **skapa**.  

3. Uppdatera slutpunkten, huvudnyckel, databas och samling värden för att ansluta till kontot och läsa tweets med kommandot spark.read.format().

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. Kör fråga för att hämta antal tweets med olika hashtaggar från cachelagrade data. 

   ```python
   %sql
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

Java SDK stöder följande värden för mappning av konfiguration: 

|Inställning  |Beskrivning  |
|---------|---------|
|query_maxdegreeofparallelism  | Anger antalet samtidiga åtgärder som körs på klientsidan under parallell frågekörning. Om det är inställt på ett värde som är större än 0, begränsar antalet samtidiga åtgärder som att det tilldelade värdet. Om det är inställt på mindre än 0, avgör systemet automatiskt antalet samtidiga åtgärder som ska köras. Det här värdet har kopplingen mappar varje samling partition med en executor, inte någon effekt på läsningen.        |
|query_maxbuffereditemcount     |    Anger det maximala antalet objekt som kan buffras på klientsidan under parallell frågekörning. Om det är inställt på ett värde som är större än 0, begränsar antalet buffrade objekt till det tilldelade värdet. Om det är inställt på mindre än 0, avgör antalet objekt till bufferten automatiskt i systemet.     |
|query_enablescan    |   Ställer in alternativet för att aktivera sökningar på frågor som inte kunde hanteras eftersom indexering har valts på de begärda sökvägarna.       |
|query_disableruperminuteusage  |  Inaktiverar programbegäran (ru: er) / minut möjligheten att leverera frågan om ordinarie etablerade ru: er/sekund är slut.       |
|query_emitverbosetraces   |   Anger att frågor för att generera ut utförlig spårningar för undersökning.      |
|query_pagesize  |   Anger storleken på resultatsidan för varje fråga. Optimera för dataflöde med en sidan är stor för att minska antalet tur och RETUR att hämta resultat för frågor.      |
|query_custom  |  Anger Azure Cosmos DB-fråga för att åsidosätta standardfrågan när data hämtas från Azure Cosmos DB. Observera att när det här värdet har angetts och den kommer att användas i stället för en fråga med flyttas fram samt predikat.     |

Beroende på scenario, ska olika konfigurationsvärden användas för att optimera prestanda och dataflöde. Observera att konfigurationsnyckeln är för närvarande skiftlägeskänslig och Konfigurationsvärdet är alltid en sträng.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Läsa twitter-data som strömmar till Azure Cosmos DB

I det här avsnittet ska köra du spark-frågor för att läsa en ändringsfeed för strömmande twitter-data. När du kör frågorna i det här avsnittet, se till att ditt Twitter-flöde app körs och också finns data till Azure Cosmos DB. HTML-versionen av den bärbara datorn är värd för den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-lagringsplatsen. Hämta databasfiler och navigera till `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html` importera anteckningsboken till ditt Azure Databricks-konto, uppdatera kontot URI, huvudnyckel, databas, samlingsnamn och köra den eller skapa anteckningsboken på följande sätt:

1. Navigera till ditt Azure Databricks-konto och välj den **arbetsytan** > **skapa** > **Notebook**.  

2. I den **Skapa anteckningsbok** dialogrutan rutan, ange ett användarvänligt namn, Välj **Scala** som språk, i listrutan väljer du klustret som du skapade tidigare och välj **skapa**.  

3. Uppdatera slutpunkten, huvudnyckel, databas och samling värden för att ansluta till kontot.

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. Starta läsning-ändringsflödet som en dataström med hjälp av kommandot spark.readStream.format():

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Starta direktuppspelning av frågan till konsolen:

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

Java SDK stöder följande värden för mappning av konfiguration:

|Inställning  |Beskrivning  |
|---------|---------|
|readchangefeed   |  Anger att innehållet hämtas från CosmosDB Change Feed. Standardvärdet är FALSKT.       |
|changefeedqueryname |   En anpassad sträng att identifiera frågan. Anslutningen håller reda på samlingen fortsättning token för separat olika ändringsflödet för frågor. Om readchangefeedis true är en konfiguration som krävs som det går inte att ta tomt värde.      |
|changefeedcheckpointlocation  |   En sökväg till lokal fillagring för att bevara fortsättning token vid nodfel.      |
|changefeedstartfromthebeginning  |  Anger om ändringsfeed ska börja från början (SANT) eller från den aktuella punkten (FALSKT). Som standard startar den från aktuellt (FALSKT).       |
|rollingchangefeed  |   Ett booleskt värde som anger om ändringsfeed ska vara från den sista frågan. Standardvärdet är false, vilket innebär att ändringarna ska räknas från den första läsningen av samlingen.      |
|changefeedusenexttoken  |   Ett booleskt värde för bearbetning av scenarier. Den används för att ange att den aktuella ändringsflödet batch har hanterats smidigt och RDD bör använda nästa fortsättning token för att hämta batch efterföljande ändringar.      |
| InferStreamSchema | Ett booleskt värde som anges om schemat för strömmande data samlas in i början av strömning. Som standard det här värdet anges till true. Om den här parametern anges till true och schemaändringar för strömmande data när data samplas, kommer att tas bort nytillagda egenskaper i dataramen strömmande. <br/><br/> Om du vill strömmande dataram vara schemaoberoende, ange den här parametern till false. I det här läget är brödtexten i dokument läsa från Azure Cosmos DB-ändringsflödet omslutna till en ”meddelandetext”-egenskap i den resulterande strömmande dataramen utöver system egenskapsvärden.
 |

### <a name="connection-settings"></a>Inställningar för anslutning

Java SDK har stöd för följande anslutningsinställningar:

|Inställning  |Beskrivning  |
|---------|---------|
|connectionmode   |  Anger vilket anslutningsläge som interna DocumentClient ska använda för att kommunicera med Azure Cosmos DB. Tillåtna värden är **DirectHttps** (standardvärde) och **Gateway**. Anslutningsläge DirectHttps dirigerar begäranden direkt till CosmosDB-partitioner och tillhandahåller vissa svarstid nytta.       |
|connectionmaxpoolsize   |  Anger värdet för poolstorlek för användaranslutning som används av interna DocumentClient. Standardvärdet är 100.       |
|connectionidletimeout  |  Anger timeout-värdet för inaktiva anslutningar i sekunder. Standardvärdet är 60.       |
|query_maxretryattemptsonthrottledrequests    |  Anger det maximala antalet återförsök. Det här värdet används vid en begäran misslyckades på grund av hastighet som begränsar på klienten. Om det inte anges är standardvärdet 1000 nya försök.       |
|query_maxretrywaittimeinseconds   |  Anger maximal tid i sekunder för försök. Som standard är den 1 000 sekunder.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Skriva twitter-data till Azure Cosmos DB 

I det här avsnittet ska köra du spark-frågor för att skriva en batch med twitter-data till en ny samling i samma databas. HTML-versionen av den bärbara datorn är värd för den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-lagringsplatsen. Hämta databasfiler och navigera till `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html` importera anteckningsboken till ditt Azure Databricks-konto, uppdatera kontot URI, huvudnyckel, databas, samlingsnamn och köra den eller skapa anteckningsboken på följande sätt:

1. Navigera till ditt Azure Databricks-konto och välj den **arbetsytan** > **skapa** > **Notebook**.  

2. I den **Skapa anteckningsbok** dialogrutan rutan, ange ett användarvänligt namn, Välj **Scala** som språk, i listrutan väljer du klustret som du skapade tidigare och välj **skapa**.  

3. Uppdatera slutpunkten, huvudnyckel, databas och samling värden för att ansluta till samlingen databas att läsa och skriva twitter-data.

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. Kör fråga för att hämta antal tweets med olika hashtaggar från cachelagrade data. 

   ```scala
   %sql
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

5. Skapa ny dataram tweets taggar och spara data till den nya samlingen. När du har kört följande kod, kan du gå tillbaka till portalen och verifiera att data skrivs till samlingen. 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

Java SDK stöder följande värden för mappning av konfiguration:

|Inställning  |Beskrivning  |
|---------|---------|
| BulkImport | Ett booleskt värde som anger om data ska importeras med hjälp av BulkExecutor-biblioteket. Som standard det här värdet anges till true. |
|WritingBatchSize  |   Anger batchstorlek ska användas för att skriva data till Azure Cosmos DB-samling. <br/><br/> Om BulkImport parametern anges till true, och sedan WritingBatchSize parametern indikerar batchstorlek av dokument som anges som indata till importAll API BulkExecutor Library. Det här värdet är som standard 100 kB. <br/><br/> Om BulkImport parametern är inställd på false, indikerar WritingBatchSize parametern batchstorlek ska användas vid skrivning till Azure Cosmos DB-samling. Kopplingen skickar begäranden om createDocument/upsertDocument asynkront i batch. Ju större batchen storlek mer dataflöde som kan uppnås så länge klusterresurserna är tillgängliga. Å andra sidan, ange ett mindre antal Gruppstorlek för att begränsa hastighet och RU-förbrukning. Skriva batchstorlek är som standard till 500.  |
|Upsert   |  Sträng booleskt värde som anger om upsertDocument ska användas i stället för CreateDocument vid skrivning till cosmos DB-samling.   |
| WriteThroughputBudget |  Ett Heltalssträng som representerar antalet RU\s som du vill tilldela bulk inmatning spark-jobb från den totala genomströmningen allokeras till samlingen. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Skriva twitter-data som strömmar till Azure Cosmos DB 

I det här avsnittet ska köra du spark-frågor för att skriva ändringsfeed för strömmande twitter-data till en ny samling i samma databas. HTML-versionen av den bärbara datorn är värd för den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-lagringsplatsen. Hämta databasfiler och navigera till `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html` importera anteckningsboken till ditt Azure Databricks-konto, uppdatera kontot URI, huvudnyckel, databas, samlingsnamn och köra den eller skapa anteckningsboken på följande sätt:

1. Navigera till ditt Azure Databricks-konto och välj den **arbetsytan** > **skapa** > **Notebook**.  

2. I den **Skapa anteckningsbok** dialogrutan rutan, ange ett användarvänligt namn, Välj **Scala** som språk, i listrutan väljer du klustret som du skapade tidigare och välj **skapa**.  

3. Uppdatera slutpunkten, huvudnyckel, databas och samling värden för att ansluta till samlingen databas att läsa och skriva twitter-data.

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. Starta läsning-ändringsflödet som en dataström med hjälp av kommandot spark.readStream.format():
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. Definiera konfigurationen av målsamling och starta det direktuppspelade jobbet med metoden writeStream.format():

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
 ```

Java SDK stöder följande värden för mappning av konfiguration:

|Inställning  |Beskrivning  |
|---------|---------|
|Upsert   |  Sträng booleskt värde som anger om upsertDocument ska användas i stället för CreateDocument vid skrivning till cosmos DB-samling.   |
|checkpointlocation  |   En sökväg till lokal fillagring för att bevara fortsättning token vid nodfel.   |
|WritingBatchSize  |  Anger batchstorlek ska användas för att skriva data till Azure Cosmos DB-samling. Kopplingen skickar begäranden om createDocument/upsertDocument asynkront i batch. Ju större batchen storlek mer dataflöde som kan uppnås så länge klusterresurserna är tillgängliga. Å andra sidan, ange ett mindre antal Gruppstorlek för att begränsa hastighet och RU-förbrukning. Skriva batchstorlek är som standard till 500.  |


## <a name="considerations-when-using-java-sdk"></a>Att tänka på när du använder Java SDK

Spark för anslutning till Azure Cosmos DB med hjälp av Java SDK rekommenderas i följande scenarier:

* Du använder Python och/eller Scala.  

* Du har en stor mängd data du överför mellan Apache Spark och Azure Cosmos DB, Java SDK har högre prestanda jämfört med pyDocumentDB. För att ge dig en uppfattning om prestandaskillnaden fråga, se den [fråga Testkörningar wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Nästa steg

Om du inte redan gjort hämta Spark till Azure Cosmos DB-anslutningen från den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-lagringsplatsen och utforska ytterligare resurser i lagringsplatsen:

* [Distribuerade aggregeringar exempel](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exempel på skript och anteckningsböcker](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Du kan också gå igenom den [Apache Spark SQL och DataFrames datauppsättningar Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html) och [Apache Spark på Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artikeln.
