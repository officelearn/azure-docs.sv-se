---
title: Apache Spark ansluta till Azure Cosmos DB | Microsoft Docs
description: Läs mer om Azure Cosmos DB Spark-anslutningsappen som gör det möjligt att ansluta Apache Spark i Azure Cosmos DB. Du kan utföra distribuerade aggregeringar av globalt distribuerade databas för flera innehavare från Microsoft.
keywords: Apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: 57ad621006a79148aaa6e9435d6ede446539c648
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714761"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Påskynda analyser av stordata med Apache Spark för Azure Cosmos DB-anslutningsapp
 
Apache Spark för Azure Cosmos DB-anslutningsapp kan Azure Cosmos DB vara indata eller utdata för Apache Spark-jobb. Ansluta [Spark](http://spark.apache.org/) till [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ökar dina möjligheter att lösa snabbrörliga data science-problem. Du kan använda Azure Cosmos DB för att snabbt spara och fråga efter data. Anslutningen används effektivt de inbyggda Azure Cosmos DB-hanterade indexen. Index kan använda uppdateringsbara kolumner när du utför analyser och push-down-predikat filtrering mot föränderliga, globalt distribuerade data. Den här typen av data kan vara mellan Internet of Things (IoT) och scenarier för datavetenskap och analys av data.

Mer information om anslutningen i den här videon:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

## <a name="connector-components"></a>Connector-komponenter

Spark kan Azure Cosmos DB-anslutningen har följande komponenter:

* [Azure Cosmos DB](http://documentdb.com) kan du etablera och Elastiskt skala både dataflöde och lagring över valfritt antal geografiska regioner.  

* [Apache Spark](http://spark.apache.org/) är en kraftfull bearbetning motor med öppen källkod, uppbyggda kring hastighet, enkel användning och avancerade analyser.  

* [Apache Spark-kluster på Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) gör det möjligt att köra Spark-jobb i Spark-klustret.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Apache Spark ansluta till Azure Cosmos DB

Det finns två sätt att ansluta Apache Spark och Azure Cosmos DB:

- [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python), en Python-baserade anslutningstjänsten, som även kallas *pyDocumentDB*.  

- [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java), en Java-baserade koppling.


**Versioner som stöds**

| Komponent | Version |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Azure Databricks-körningsversion | > 3.4 |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Ansluta med hjälp av Python eller pyDocumentDB SDK

Följande diagram illustrerar arkitekturen i SDK-implementering pyDocumentDB:

![Diagram över Spark på Azure Cosmos DB dataflöde via pyDocumentDB DB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Dataflöde

Dataflöde för implementeringen av pyDocumentDB är följande:

* Huvudnoden Spark ansluter till Azure Cosmos DB gateway-noden via pyDocumentDB. Du anger Spark och Azure Cosmos DB anslutningarna bara. Anslutningar till respektive huvud- och gateway-noderna är transparent.  

* Gateway-noden gör frågan mot Azure Cosmos DB, där frågan därefter körs mot samlingens partitioner i datanoderna. Svaret för dessa frågor skickas tillbaka till gateway-noden och den resultatuppsättningen returneras till Spark-huvudnoden.  

* Efterföljande frågor (till exempel mot Spark data frame) skickas till arbetsnoder Spark för bearbetning.  

Kommunikation mellan Spark och Azure Cosmos DB är begränsad till Spark-huvudnod och Azure Cosmos DB gateway-noder. Frågorna gå så snabbt som gör att transportlagret mellan dessa två noder.

Kör du följande steg för att ansluta Spark till Azure Cosmos DB med hjälp av pyDocumentDB SDK:

1. Skapa en [Azure Databricks-arbetsytan](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) och en [Spark-kluster](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Azure Databricks körningsversion 4.0 innehåller Apache Spark 2.3.0 och Scala 2.11 ingår i den arbetsytan.  

2. När klustret har skapats och körs, går du till **arbetsytan** > **skapa** > **biblioteket**.  
3. Dialogrutan Nytt bibliotek väljer **ladda upp Python ägg eller PyPi** som källa. Ange **pydocumentdb** som namn och välj **installera biblioteket**. pyDocumentdb SDK har redan publicerats till pip-paketen, så du kan hitta och installera den. 

   ![Skärmbild av dialogrutan Nytt bibliotek](./media/spark-connector/create-library.png)

4. När biblioteket har installerats kan du koppla den till klustret du skapade tidigare.  

5. Gå till **arbetsytan** > **skapa** > **Notebook**.  

6. I den **Skapa anteckningsbok** dialogrutan, ange ett användarvänligt namn och välj **Python** som språk. Från listrutan, Välj det kluster som du skapade tidigare och välj **skapa**.  

7. Kör några Spark-frågor med hjälp av flyg exempeldata finns i ”doctorwho” Azure Cosmos DB-konto. (Det här kontot är offentligt tillgänglig.) Den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-lagringsplatsen är värd för HTML-versionen av anteckningsboken. Hämta databasfiler för och gå till `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html`. Du kan importera anteckningsboken till ditt Azure Databricks-konto och kör den. I följande avsnitt beskrivs funktioner i kodblock i detalj.

Följande kodfragment visar hur du importerar pyDocumentDB SDK och köra en fråga i Spark-kontext. Enligt vad som anges i kodfragmentet innehåller pyDocumentDB SDK anslutningsparametrar som krävs för att ansluta till Azure Cosmos DB-konto. Det importerar bibliotek som krävs och konfigurerar huvudnyckel och värden för att skapa Azure Cosmos DB-klienten (pydocumentdb.document_client).


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

Du kan sedan köra frågor. Följande kodfragment ansluter till samlingen airports.codes i doctorwho-konto och kör en fråga för att extrahera flygplats städer i delstaten Washington. 

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

När du har kört frågan är resultatet en ”query_iterable. QueryIterable ”som konverteras till en Python-lista. Den här listan i sin tur konverteras till Spark data frame. 

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

### <a name="considerations-when-using-pydocumentdb-sdk"></a>Att tänka på när du använder pyDocumentDB SDK
Ansluta Spark till Azure Cosmos DB med hjälp av pyDocumentDB SDK rekommenderas i följande scenarier:

* Du vill använda Python.  

* Du returnerar ett relativt litet resultat från Azure Cosmos DB på Spark. Observera att den underliggande datauppsättningen i Azure Cosmos DB kan vara ganska stor och du tillämpa filter eller kör predikat filter mot Azure Cosmos DB-källa.

## <a name="connect-by-using-the-java-sdk"></a>Ansluta med hjälp av Java SDK

Följande diagram illustrerar arkitekturen i Azure Cosmos DB SQL Java SDK-implementering och flyttas data mellan arbetsnoder Spark:

![Diagram över dataflöde i Spark kan Azure Cosmos DB-anslutningsapp](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Dataflöde

Dataflöde för Java SDK-implementeringen är följande:

* Spark-huvudnoden ansluter till Azure Cosmos DB gateway-noden att hämta partition kartan. Du anger bara Spark och Azure Cosmos DB-anslutningar. Anslutningar till respektive huvud- och gateway-noderna är transparent.  

* Den här informationen tillhandahålls tillbaka till Spark-huvudnoden. Du bör nu kunna parsa frågan för att fastställa partitionerna och deras platser i Azure Cosmos DB som du behöver komma åt.  

* Den här informationen skickas till arbetsnoder Spark.  

* Spark-arbetsnoder ansluta till Azure Cosmos DB-partitioner direkt, för att extrahera och returnera data till Spark-partitioner i arbetsnoderna.  

Kommunikation mellan Spark och Azure Cosmos DB är betydligt snabbare, eftersom dataförflyttning mellan arbetsnoder Spark och Azure Cosmos DB-datanoder (partitioner). I det här exemplet skickar Twitter-exempeldata till Azure Cosmos DB-kontot och köra Spark-frågor med hjälp av dessa data. Använd följande steg för att skriva exempel Twitter-data till Azure Cosmos DB:

1. Skapa en [Azure Cosmos DB SQL API-konto](create-sql-api-dotnet.md#create-a-database-account) och [Lägg till en samling](create-sql-api-dotnet.md#add-a-collection) till kontot.  

2. Ladda ned den [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) exemplet från GitHub. Du kan använda den här feeden för att skriva exempel Twitter-data till Azure Cosmos DB.  

3. Öppna en kommandotolk och installera Tweepy och pyDocumentdb moduler genom att köra följande kommandon:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Extrahera innehållet i Twitter-flöde exemplet och öppna filen config.py. Uppdatera masterKey, värden, databaseId, collectionId och preferredLocations värden.  

5. Gå till `http://apps.twitter.com/`, och registrera den Twitter-flöde skriptet som ett nytt program. När du har valt ett namn och program för din app, visas med en **använda nyckeln, konsumenthemligheten, åtkomst-token och åtkomsttokenhemligheten**. Kopiera dessa värden och uppdatera dem i config.py filen att ge programmet programmässig åtkomst till Twitter.   

6. Spara filen config.py. Öppna en kommandotolk och kör Python-program med hjälp av följande kommando:

   ```bash
   Python driver.py
   ```

7. Gå till Azure Cosmos DB-samling i portalen och kontrollera att Twitter-data skrivs till samlingen.

### <a name="find-and-attach-the-java-sdk-to-the-spark-cluster"></a>Hitta och ansluta Java SDK till Spark-kluster

1. Skapa en [Azure Databricks-arbetsytan](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) och en [Spark-kluster](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Azure Databricks körningsversion 4.0 innehåller Apache Spark 2.3.0 och Scala 2.11 ingår i den arbetsytan.  

2. När klustret har skapats och körs, går du till **arbetsytan** > **skapa** > **biblioteket**.  

3. Från den **nytt bibliotek** dialogrutan väljer du **Maven-koordinat** som källa. Ange värdet koordinaten **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**, och välj **skapa bibliotek**. Maven-beroenden har lösts och paketet har lagts till i din arbetsyta. I föregående Maven koordinaten format, 2.3.0 representerar Spark-version och 2.11 representerar Scala-version 1.2.0 representerar den Azure Cosmos DB connector-versionen. 

4. När biblioteket har installerats kan du koppla den till klustret du skapade tidigare. 

Den här artikeln visar hur du använder Spark-anslutningsappen Java SDK i följande scenarier:

* Läsa Twitter-data från Azure Cosmos DB.  

* Läs Twitter-data som strömmar till Azure Cosmos DB.  

* Skriva Twitter-data till Azure Cosmos DB. 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Läsa Twitter-data från Azure Cosmos DB
 
I det här avsnittet ska köra du Spark frågor för att läsa en batch med Twitter-data från Azure Cosmos DB. Den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-lagringsplatsen är värd för HTML-versionen av anteckningsboken. Hämta databasfiler för och gå till `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html`. Du kan importera anteckningsboken till ditt Azure Databricks-konto och uppdatera kontot URI, huvudnyckel, databas och samlingsnamn. Du kan köra anteckningsboken eller skapa den på följande sätt:

1. Gå till ditt Azure Databricks-konto och välj **arbetsytan** > **skapa** > **Notebook**. 

2. I den **Skapa anteckningsbok** dialogrutan, ange ett användarvänligt namn och välj **Python** som språk. Från listrutan, Välj det kluster som du skapade tidigare och välj **skapa**.  

3. Uppdatera slutpunkten, huvudnyckel, databas och samling värden för att ansluta till kontot. Läsa tweets med hjälp av kommandot spark.read.format().

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
|query_enablescan    |   Ställer in alternativet för att aktivera sökningar på de frågor som inte kunde hanteras eftersom indexering har valts på de begärda sökvägarna.       |
|query_disableruperminuteusage  |  Inaktiverar enheter för programbegäran (ru: er) / minut möjligheten att leverera frågan, om regular etablerade ru: er/sekund är slut.       |
|query_emitverbosetraces   |   Anger att frågor för att generera ut utförlig spårningar för undersökning.      |
|query_pagesize  |   Anger storleken på resultatsidan för varje fråga. Använda en sidan är stor för att minska antalet tur och RETUR att hämta resultat för att optimera genomflödet.      |
|query_custom  |  Anger Azure Cosmos DB-fråga för att åsidosätta standardfrågan när data hämtas från Azure Cosmos DB. Observera att när du anger det här värdet används det i stället en fråga med flyttas fram samt predikat.     |

Beroende på scenario, bör du använda olika konfigurationsvärden för att optimera prestanda och dataflöde. Observera att konfigurationsnyckeln är för närvarande skiftlägeskänslig och Konfigurationsvärdet är alltid en sträng.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Läs Twitter-data som strömmar till Azure Cosmos DB

I det här avsnittet ska köra du Spark frågor för att läsa en ändringsfeed för strömmande Twitter-data. När du kör frågorna i det här avsnittet, se till att ditt Twitter-flöde app körs och också finns data till Azure Cosmos DB. Den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-lagringsplatsen är värd för HTML-versionen av anteckningsboken. Hämta databasfiler för och gå till `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html`. Du kan importera anteckningsboken till ditt Azure Databricks-konto och uppdatera kontot URI, huvudnyckel, databas och samlingsnamn. Du kan köra anteckningsboken eller skapa den på följande sätt:

1. Gå till ditt Azure Databricks-konto och välj **arbetsytan** > **skapa** > **Notebook**.  

2. I den **Skapa anteckningsbok** dialogrutan, ange ett användarvänligt namn och välj **Scala** som språk. Från listrutan, Välj det kluster som du skapade tidigare och välj **skapa**.  

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
4. Börja läsa ändringen feed som en dataström med hjälp av kommandot spark.readStream.format():

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
|changefeedqueryname |   En anpassad sträng att identifiera frågan. Anslutningen håller reda på samlingen fortsättning token för separat olika ändringsflödet för frågor. Om readchangefeed stämmer är det här en konfiguration som det går inte att ta tomt värde.      |
|changefeedcheckpointlocation  |   En sökväg till lokal fillagring för att bevara fortsättning token vid nodfel.      |
|changefeedstartfromthebeginning  |  Anger om ändringsfeed ska börja från början (SANT) eller från den aktuella punkten (FALSKT). Som standard startar den från aktuellt (FALSKT).       |
|rollingchangefeed  |   Ett booleskt värde som anger om ändringsfeed ska vara från den sista frågan. Standardvärdet är false, vilket innebär att ändringarna räknas från den första läsningen av samlingen.      |
|changefeedusenexttoken  |   Ett booleskt värde för bearbetning av scenarier. Indikerar att den aktuella ändringsflödet batch har hanterats ett smidigt sätt. Resiliant Distributed Dataset bör använda nästa fortsättning-tokens för att hämta batch efterföljande ändringar.      |
| InferStreamSchema | Ett booleskt värde som anger om schemat för strömmande data samlas in i början av strömning. Som standard det här värdet anges till true. Om den här parametern anges till true och schemaändringar för strömmande data när data samplas, kommer att tas bort nytillagda egenskaper i dataramen strömmande. <br/><br/> Om du vill strömmande dataram vara schemaoberoende, ange den här parametern till false. I det här läget är brödtexten i dokument läsa från Azure Cosmos DB-ändringsflödet omslutna till en brödtext-egenskap. Den här egenskapen är i den resulterande strömmande dataramen, förutom system egenskapsvärden.
 |

### <a name="connection-settings"></a>Inställningar för anslutning

Java SDK har stöd för följande anslutningsinställningar:

|Inställning  |Beskrivning  |
|---------|---------|
|connectionmode   |  Anger vilket anslutningsläge som interna DocumentClient ska använda för att kommunicera med Azure Cosmos DB. Tillåtna värden är **DirectHttps** (standardvärde) och **Gateway**. Anslutningsläge DirectHttps dirigerar begäranden direkt till CosmosDB-partitioner och tillhandahåller vissa svarstid nytta.       |
|connectionmaxpoolsize   |  Anger värdet för poolstorlek för användaranslutning som används av interna DocumentClient. Standardvärdet är 100.       |
|connectionidletimeout  |  Anger timeout-värdet i sekunder för inaktiva anslutningar. Standardvärdet är 60.       |
|query_maxretryattemptsonthrottledrequests    |  Anger det maximala antalet återförsök. Du använder det här värdet när det gäller en begäran misslyckades på grund av hastighet som begränsar på klienten. Om det inte anges är standardvärdet 1000 nya försök.       |
|query_maxretrywaittimeinseconds   |  Anger maximal tid i sekunder för försök. Som standard är den 1 000 sekunder.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Skriva Twitter-data till Azure Cosmos DB 

I det här avsnittet ska köra du Spark frågor för att skriva en batch med Twitter-data till en ny samling i samma databas. Den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-lagringsplatsen är värd för HTML-versionen av anteckningsboken. Hämta databasfiler för och gå till `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html`. Du kan importera anteckningsboken till ditt Azure Databricks-konto och uppdatera kontot URI, huvudnyckel, databas och samlingsnamn. Du kan köra anteckningsboken eller skapa den på följande sätt:

1. Gå till ditt Azure Databricks-konto och välj **arbetsytan** > **skapa** > **Notebook**.  

2. I den **Skapa anteckningsbok** dialogrutan, ange ett användarvänligt namn och välj **Scala** som språk. Från listrutan, Välj det kluster som du skapade tidigare och välj **skapa**.  

3. Uppdatera slutpunkten, huvudnyckel, databas och samling värden för att ansluta till samlingen databas att läsa och skriva Twitter-data.

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

5. Skapa en ny dataram tweets taggar och spara data till den nya samlingen. När du har kört följande kod, kan du gå tillbaka till portalen och verifiera att data skrivs till samlingen. 

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
|WritingBatchSize  |   Anger batchstorlek ska användas när du skriver data till Azure Cosmos DB-samling. <br/><br/> Om BulkImport parametern anges till true, och sedan WritingBatchSize parametern indikerar batchstorlek av dokument som anges som indata till importAll API BulkExecutor Library. Det här värdet är som standard 100 kB. <br/><br/> Om BulkImport parametern är inställd på false, indikerar WritingBatchSize parametern batchstorlek ska användas när du skriver till Azure Cosmos DB-samling. Kopplingen skickar begäranden om createDocument och upsertDocument asynkront i batch. Ju större batchstorleken, större dataflöde som du kan uppnå, så länge klusterresurserna är tillgängliga. Å andra sidan, ange ett mindre antal Gruppstorlek för att begränsa hastighet och RU-förbrukning. Skriva batchstorlek är som standard till 500.  |
|Upsert   |  Sträng booleskt värde som anger om du vill använda upsertDocument i stället för CreateDocument när du skriver till Azure Cosmos DB-samling.   |
| WriteThroughputBudget |  Ett Heltalssträng som representerar antalet RU\s som du vill tilldela bulk inmatning Spark-jobb, utanför den totala genomströmningen allokeras till samlingen. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Skriva Twitter-data som strömmar till Azure Cosmos DB 

I det här avsnittet ska köra du Spark frågor för att skriva en ändringsfeed för strömmande Twitter-data till en ny samling i samma databas. Den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-lagringsplatsen är värd för HTML-versionen av anteckningsboken. Hämta databasfiler för och gå till `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html`. Du kan importera anteckningsboken till ditt Azure Databricks-konto och uppdatera kontot URI, huvudnyckel, databas och samlingsnamn. Du kan köra anteckningsboken eller skapa den på följande sätt:

1. Gå till ditt Azure Databricks-konto och välj **arbetsytan** > **skapa** > **Notebook**.  

2. I den **Skapa anteckningsbok** dialogrutan, ange ett användarvänligt namn och välj **Scala** som språk. Från listrutan, Välj det kluster som du skapade tidigare och välj **skapa**.  

3. Uppdatera slutpunkten, huvudnyckel, databas och samling värden för att ansluta till samlingen databas att läsa och skriva Twitter-data.

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
4. Börja läsa ändringen feed som en dataström med hjälp av kommandot spark.readStream.format():
 
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
|Upsert   |  Sträng booleskt värde som anger om du vill använda upsertDocument i stället för CreateDocument när du skriver till Azure Cosmos DB-samling.   |
|checkpointlocation  |   En sökväg till lokal fillagring för att bevara fortsättning token vid nodfel.   |
|WritingBatchSize  |  Anger batchstorlek ska användas för att skriva data till Azure Cosmos DB-samling. Kopplingen skickar begäranden om createDocument och upsertDocument asynkront i batch. Ju större batchstorleken, större dataflöde som du kan uppnå, så länge klusterresurserna är tillgängliga. Å andra sidan, ange ett mindre antal Gruppstorlek för att begränsa hastighet och RU-förbrukning. Skriva batchstorlek är som standard till 500.  |


### <a name="considerations-when-using-java-sdk"></a>Att tänka på när du använder Java SDK

Ansluta Spark till Azure Cosmos DB med hjälp av Java SDK rekommenderas i följande scenarier:

* Du vill använda Python och Scala.  

* Du har en stor mängd data du överför mellan Apache Spark och Azure Cosmos DB. Java SDK presterar bättre än pyDocumentDB. Mer information om prestandaskillnaden frågan finns i den [frågetest körs wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Nästa steg

Om du inte redan gjort hämta Spark till Azure Cosmos DB-anslutningen från den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-lagringsplatsen. Utforska följande ytterligare resurser i lagringsplatsen:

* [Aggregeringar exempel](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exempel på skript och anteckningsböcker](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Du kan också gå igenom den [Apache Spark SQL och DataFrames datauppsättningar Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html), och [Apache Spark på Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artikeln.
