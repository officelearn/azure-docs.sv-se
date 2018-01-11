---
title: Ansluta Apache Spark till Azure Cosmos DB | Microsoft Docs
description: "Använd den här självstudiekursen vill veta mer om Azure Cosmos DB Spark-koppling kan du ansluta Apache Spark i Azure Cosmos DB att utföra distribuerade aggregeringar och data sciences på flera innehavare globalt distribuerade databassystemet från Microsoft som utformad för molnet."
keywords: Apache spark
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: denlee
ms.openlocfilehash: 4ba8a53f2018727cc4fa225b2d4ce14d9f1d7467
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Påskynda realtid stordata med Spark på Azure DB som Cosmos-kopplingen

Spark på Azure DB som Cosmos-kopplingen kan Azure Cosmos-Databsen ska fungera som en Indatakällan eller utdatamottagaren för Apache Spark-jobb. Ansluta [Spark](http://spark.apache.org/) till [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) accelererar möjligheten att lösa fast flyttar datavetenskap problem där du kan använda Azure Cosmos DB snabbt bevara och fråga efter data. Spark på Azure DB som Cosmos-kopplingen använder effektivt interna Azure Cosmos DB hanteras index. Index aktivera uppdateringsbara kolumner när du utföra analyser och push-ned predikat filtrering mot fast föränderliga globalt distribueras data mellan Sakernas Internet (IoT) till scenarion för vetenskap och analys av data.

Läs mer i den här videon med Azure Cosmos DB högsta programansvarig Denny Pettersson. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

För att arbeta med Spark GraphX och Gremlin graph API: er i Azure Cosmos DB, se [utföra diagrammet analytics med hjälp av Spark och Apache TinkerPop Gremlin](spark-connector-graph.md).

## <a name="download"></a>Ladda ned

Kom igång genom att ladda ned Spark till Azure DB som Cosmos-anslutningen från den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/) databasen på GitHub.

## <a name="connector-components"></a>Connector-komponenter

Anslutningen använder följande komponenter:

* [Azure Cosmos-DB](http://documentdb.com) ger kunder möjlighet att etablera och skala Elastiskt både dataflöden och lagringsutrymmen till alla geografiska regioner. Tjänsten innehåller:
   * Aktivera nyckeln [global distributionsplatsen](distribute-data-globally.md) och skala horisontellt
   * Garanteras siffra fördröjningar vid 99th percentilen
   * [Flera väldefinierade konsekvenskontroll modeller](consistency-levels.md)
   * Garanterat hög tillgänglighet med flera funktioner
   * Alla funktioner backas upp av branschledande, omfattande [serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLA).

* [Apache Spark](http://spark.apache.org/) är en kraftfull öppen källkod bearbetningsmotor som är uppbyggd kring hastighet, enkel användning och avancerade analyser.

* [Apache Spark på Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) så att du kan distribuera Apache Spark i molnet för verksamhetskritiska distributioner med hjälp av [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Officiellt versioner som stöds:

| Komponent | Version |
|---------|-------|
|Apache Spark|2.0.2, 2.1.0, 2.2.0|
| Scala| 2.10, 2.11|
| Azure Cosmos DB SQL Java SDK | 1.14.0, 1.15.0 |

Den här artikeln hjälper dig att köra några enkla exempel med hjälp av Python (via pyDocumentDB) och Scala-gränssnitt.

Det finns två sätt att ansluta Apache Spark och Azure Cosmos DB:
- Använd pyDocumentDB via den [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python).
- Skapa en Java-baserad Spark på Azure Cosmos DB kopplingen genom att använda den [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>pyDocumentDB implementering
Aktuellt [pyDocumentDB SDK](https://github.com/Azure/azure-documentdb-python) kan du ansluta Spark i Azure Cosmos DB som visas i följande diagram:

![Väck på Azure Cosmos DB dataflöde via pyDocumentDB DB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Dataflöde för pyDocumentDB-implementering

Dataflödet är följande:

1. Noden som Spark ansluter till Azure Cosmos DB gateway-noden via pyDocumentDB. En användare anger endast Spark och Azure Cosmos DB-anslutningar. Anslutningar till respektive huvud- och gateway-noderna är transparent för användaren.
2. Gateway-noden gör frågan mot Azure Cosmos DB där frågan därefter körs mot mängdens partitioner i datanoder. Svaret på dessa frågor skickas tillbaka till gateway-noden och att resultatet returneras till huvudnoden Spark.
3. Efterföljande frågor (till exempel mot ett Spark-DataFrame) skickas till arbetsnoder Spark för bearbetning.

Kommunikation mellan Spark och Azure Cosmos DB är begränsad till Spark-huvudnod och Azure Cosmos DB gateway-noder.  Frågorna går snabbt Transportskiktet mellan dessa två noder kan.

### <a name="install-pydocumentdb"></a>Installera pyDocumentDB
Du kan installera pyDocumentDB på Drivrutinsnoden med hjälp av **pip**, till exempel:

```bash
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>Ansluta Spark i Azure Cosmos DB via pyDocumentDB
Enkelhet kommunikation transport gör körningen av en fråga från Spark i Azure Cosmos DB genom att använda relativt enkla pyDocumentDB.

Följande kodavsnitt visar hur du använder pyDocumentDB i en kontext som Spark.

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

Enligt beskrivningen i kodfragmentet:

* Azure Cosmos DB Python SDK (`pyDocumentDB`) innehåller alla de nödvändiga anslutningsparametrarna. Parametern önskade platser väljer exempelvis skrivskyddade replik och prioritet ordningsföljden.
*  Importera de nödvändiga bibliotek och konfigurera din **masterKey** och **värden** att skapa Azure Cosmos DB *klienten* (**pydocumentdb.document_client** ).


### <a name="execute-spark-queries-via-pydocumentdb"></a>Köra Spark-frågor via pyDocumentDB
I följande exempel används Azure DB som Cosmos-instans som skapades i föregående fragment med angivna nycklar i skrivskyddat läge. Följande kodavsnitt som ansluter till den **airports.codes** samling i DoctorWho kontot som angavs tidigare och kör en fråga om du vill extrahera flygplats orter i staten Washington.

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

Efter att frågan har utförts **frågan**, resultatet är en **query_iterable. QueryIterable** som konverteras till en Python-lista. En lista med Python kan enkelt omvandlas till ett Spark-DataFrame med hjälp av följande kod:

```python
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>Varför ska jag använda pyDocumentDB ansluta Spark i Azure Cosmos DB?
Ansluter Spark i Azure Cosmos DB med hjälp av pyDocumentDB är vanligtvis för scenarier där:

* Du vill använda Python.
* Du returnerar relativt små resultat från Azure Cosmos DB till Spark. Observera att den underliggande datamängden i Azure Cosmos DB kan vara ganska stor. Du kopplar filter, det vill säga kör predikat filter mot dina Azure Cosmos DB-datakälla.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Väck till Azure DB som Cosmos-koppling

Spark på Azure DB som Cosmos-kopplingen använder den [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java) och flyttar data mellan Spark arbetarnoder och Azure Cosmos DB som visas i följande diagram:

![Dataflödet i Spark på Azure DB som Cosmos-kopplingen](./media/spark-connector/spark-connector.png)

Dataflödet är följande:

1. Noden som Spark ansluter till Azure Cosmos DB gateway-noden för att hämta partitionen kartan. En användare anger endast Spark och Azure Cosmos DB-anslutningar. Anslutningar till respektive huvud- och gateway-noderna är transparent för användaren.
2. Den här informationen tillbaka till noden som Spark.  Du bör nu kunna parsa frågan för att fastställa partitionerna och deras platser i Azure Cosmos-databasen som du behöver åtkomst till.
3. Den här informationen skickas till arbetsnoder Spark.
4. Arbetsnoderna Spark ansluta till Azure Cosmos DB partitioner direkt för att extrahera data och returnera data till Spark-partitioner i Spark arbetsnoderna.

Kommunikation mellan Spark och Azure Cosmos DB är betydligt snabbare eftersom data flyttas mellan arbetarnoder Spark och Azure Cosmos DB datanoder (partitioner).

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Skapa Spark på Azure DB som Cosmos-kopplingen
För närvarande använder connector projektet maven. Du kan köra för att skapa anslutningen utan beroenden:
```
mvn clean package
```
Du kan också hämta de senaste versionerna av JAR från den [ *släpper* mappen](https://github.com/Azure/azure-cosmosdb-spark/releases).

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Inkludera Azure Cosmos DB Spark JAR
Innan du kan köra all kod som du behöver inkludera JAR till Spark för Azure Cosmos DB.  Om du använder den **spark-shell**, och du kan inkludera JAR med hjälp av den **--burkar** alternativet.  

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0-uber.jar
```

Om du vill köra JAR utan beroenden, använder du följande kod:

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar,/$location/azure-documentdb-1.14.0.jar,/$location/azure-documentdb-rx-0.9.0-rc2.jar,/$location/json-20140107.jar,/$location/rxjava-1.3.0.jar,/$location/rxnetty-0.4.20.jar 
```

Om du använder en bärbar dator tjänst, till exempel Azure HDInsight Jupyter-anteckningsbok service kan du använda den **Väck magic** kommandon:

```
%%configure
{ "name":"Spark-to-Cosmos_DB_Connector", 
  "jars": ["wasb:///example/jars/1.0.0/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-1.14.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-rx-0.9.0-rc2.jar", "wasb:///example/jars/1.0.0/json-20140107.jar", "wasb:///example/jars/1.0.0/rxjava-1.3.0.jar", "wasb:///example/jars/1.0.0/rxnetty-0.4.20.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

Den **burkar** kommandot kan du inkludera två burkar som behövs för **azure cosmosdb spark** (sig själv och Azure Cosmos SQL DB Java SDK) och utelämna **scala-återspeglar**så att den inte stör Livius-anrop (Jupyter-anteckningsbok > Livius > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>Ansluta Spark till Azure Cosmos-databasen med anslutningen
Även om kommunikationen transporten är lite mer komplicerad, är kör en fråga från Spark i Azure Cosmos DB med hjälp av anslutningen betydligt snabbare.

Följande kodavsnitt visar hur du använder kopplingen i en Spark-session. Se `azure-cosmosdb-spark` [GitHub-repo-](https://github.com/Azure/azure-cosmosdb-spark) för Python prover.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val baseConfig = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(baseConfig)
coll.createOrReplaceTempView("c")
```

Enligt beskrivningen i kodfragmentet:

- **Azure cosmosdb spark** innehåller alla de nödvändiga anslutningsparametrarna, bland annat de primära platserna. Exempelvis kan du välja den skrivskyddade replik och prioritet ordningen.
- Bara importera de nödvändiga bibliotek och konfigurera masterKey och värden för att skapa Azure DB som Cosmos-klienten.

### <a name="execute-spark-queries-via-the-connector"></a>Köra Spark-frågor via connector

I följande exempel används Azure DB som Cosmos-instans som skapades i föregående fragment med angivna nycklar i skrivskyddat läge. Följande kodavsnitt ansluter till samlingen DepartureDelays.flights_pcoll (i det DoctorWho kontot som angavs tidigare) och kör en fråga om du vill extrahera flyginformation för fördröjning av flygplan reser från Seattle.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>Varför använda Spark Azure Cosmos DB-implementeringen för anslutningen?

Ansluta Spark i Azure Cosmos DB med hjälp av anslutningen är vanligtvis för scenarier där:

* Du vill använda Python och/eller Scala.
* Du har en stor mängd data för överföring mellan Apache Spark och Azure Cosmos DB.

För att ge dig en uppfattning om frågan prestandaskillnaden, finns det [frågan Testkörningar wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Distribuerade aggregeringsexempel
Det här avsnittet innehåller några exempel på hur du kan göra distribuerade aggregeringar och analytics med hjälp av Apache Spark och Azure Cosmos DB tillsammans. Azure Cosmos-DB stöder redan aggregeringar, som beskrivs i den [planeten skala mängder med Azure Cosmos DB blogg](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/). Här är hur du kan dra den till nästa nivå med Apache Spark.

Observera att dessa aggregeringar är reference till den [Spark till Azure Cosmos DB Connector anteckningsboken](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb).

### <a name="connect-to-flights-sample-data"></a>Ansluta till flygplan exempeldata
Exemplen aggregering åtkomst till vissa svarta prestandadata som är lagrad i vår **DoctorWho** Azure Cosmos-DB-databas. För att ansluta till den, måste du använda följande kodavsnitt:

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Med den här fragment kommer vi också att köra en bas-fråga som överför en filtrerad uppsättning data från Azure Cosmos DB till Spark där denna kan utföra distribuerade mängder. I det här fallet ber vi om flygplan avviker från Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Följande resultat har skapats genom att köra frågor från Jupyter-anteckningsbok tjänsten.  Observera att alla kodavsnitt är generisk och inte är specifika för varje tjänst.

### <a name="running-limit-and-count-queries"></a>Körs GRÄNSEN och antal frågor
Precis som du ofta i SQL/Spark SQL, låt oss börja med en **GRÄNSEN** fråga:

![Spark GRÄNSEN fråga](./media/spark-connector/spark-sql-query.png)

Nästa fråga är en enkel och snabb **antal** fråga:

![Spark COUNT-frågan](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>GROUP BY-fråga
I den här nästa, vi kan köras **GROUP BY** frågor mot vår Azure Cosmos-DB-databas:

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Spark GROUP BY frågan diagram](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>DISTINKT ORDER BY-fråga
Och här är en **DISTINCT, ORDER BY** fråga:

![Spark GROUP BY frågan diagram](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Fortsätta svarta dataanalys
Du kan använda följande Exempelfrågor för att fortsätta analys av data rör sig:

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Vanliga 5 fördröjd mål (orter) reser från Seattle
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Spark översta fördröjningar diagram](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Beräkna median fördröjningar av mål orter reser från Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Spark median fördröjningar diagram](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>Nästa steg

Om du inte redan gjort ladda ned Spark till Azure DB som Cosmos-anslutningen från den [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-lagringsplatsen och utforska ytterligare resurser i lagringsplatsen:

* [Distribuerade aggregeringar exempel](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exempel på skript och bärbara datorer](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Du kanske också vill granska den [datauppsättningar Guide, DataFrames och Apache Spark SQL](http://spark.apache.org/docs/latest/sql-programming-guide.html) och [Apache Spark på Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artikel.
