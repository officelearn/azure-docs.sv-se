---
title: Arbeta med Azure Cosmos DB Cassandra-API från Spark
description: Den här artikeln är huvudsidan för Cosmos DB Cassandra API-integration från Spark.
services: cosmos-db
author: anagha-microsoft
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: f2f5aebf32cf5860ca8fc32ab741177c6df15c60
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227205"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Ansluta till Azure Cosmos DB Cassandra-API från Spark

Den här artikeln är en mellan en serie artiklar om Azure Cosmos DB Cassandra API-integrering från Spark. Artiklarna beskriver anslutningen, Data Definition Language(DDL) åtgärder, grundläggande Data Manipulation Language(DML) åtgärder och avancerade Azure Cosmos DB Cassandra API-integration från Spark. 

## <a name="prerequisites"></a>Förutsättningar
* [Etablera ett Cassandra-API för Azure Cosmos DB-konto.](create-cassandra-dotnet.md#create-a-database-account)

* Etablera ditt val av Spark-miljö [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Övriga].

## <a name="dependencies-for-connectivity"></a>Beroenden för anslutning
* **Spark-anslutningappen för Cassandra:** Spark-anslutningsappen som används för att ansluta till Azure Cosmos DB Cassandra-API.  Identifiera och använda versionen av anslutningsappen finns i [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) som är kompatibel med Spark och Scala-versioner av Spark-miljö.

* **Azure Cosmos DB-hjälpbibliotek för Cassandra-API:** förutom Spark-anslutningsappen måste en annan library, även kallat [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) från Azure Cosmos DB. Det här biblioteket innehåller en anslutningsfabrik och en princip för anpassad återförsöksklasser.

  Återförsöksprincip i Azure Cosmos DB har konfigurerats för att hantera HTTP-status kod 429 (”begär Rate stor”) undantag. Azure Cosmos DB Cassandra API översätter sådana undantag till överbelastade fel på protokollet i Cassandra och du kan göra med tillbaka på de. Eftersom Azure Cosmos DB använder etablerat dataflöde modellen kan inträffa begäran rate begränsande undantag när ingång/utgång bedömer ökning. Återförsöksprincipen som skyddar ditt spark-jobb mot data toppar som tillfälligt överskrider det dataflöde som allokerats för din samling.

  > [!NOTE] 
  > Återförsöksprincipen kan skydda ditt spark-jobb mot tillfälliga toppar. Om du inte har konfigurerat tillräckligt med ru: er som krävs för att köra arbetsbelastningen återförsöksprincipen gäller inte och försök principklass skickas tillbaka undantaget.

* **Anslutningsinformationen för Azure Cosmos DB-konto:** Cassandra API för din Azure-kontonamn, slutpunkten-konto och nyckel.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Konfigurationsparametrar för Spark connector dataflöde

I följande tabell visas Azure Cosmos DB Cassandra API-specifika dataflöde konfigurationsparametrar som tillhandahålls av anslutningen. En detaljerad lista över alla konfigurationsparametrar Se [adresskonfigurationen som refereras till](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) för Spark Cassandra Connector GitHub-lagringsplatsen.

| **Egenskapsnamn** | **Standardvärde** | **Beskrivning** |
|---------|---------|---------|
| Spark.cassandra.Output.batch.Size.Rows |  1 |Antal rader per enskild batch. Ange den här parametern till 1. Den här parametern används för att uppnå högre genomströmning för tunga arbetsbelastning. |
| Spark.cassandra.connection.connections_per_executor_max  | Ingen | Maximalt antal anslutningar per nod och executor. 10 * n motsvarar 10 anslutningar per nod i ett n-nod Cassandra-kluster. Så om du behöver 5 anslutningar per nod och executor för en 5 nod Cassandra-kluster, bör du ange den här konfigurationen till 25. Ändra det här värdet baserat på graden av parallellitet eller antalet körare som spark-jobb har konfigurerats för.   |
| Spark.cassandra.Output.Concurrent.Writes  |  100 | Anger hur många parallella skrivningar som kan uppstå per executor. Eftersom du har angett ”batch.size.rows” till 1 kan du se till att skala upp det här värdet i enlighet med detta. Ändra det här värdet baserat på graden av parallellitet eller det dataflöde som du vill uppnå för din arbetsbelastning. |
| Spark.cassandra.Concurrent.Reads |  512 | Anger hur många parallella läsningar som kan uppstå per executor. Ändra värdet baserat på graden av parallellitet eller det dataflöde som du vill uppnå för din arbetsbelastning  |
| Spark.cassandra.Output.throughput_mb_per_sec  | Ingen | Anger totalt antal skrivåtgärder dataflöde per executor. Den här parametern kan användas som ett övre begränsa för ditt spark-jobb dataflöde och baseras på det etablerade dataflödet på din Cosmos DB-samling.   |
| Spark.cassandra.Input.reads_per_sec| Ingen   | Definierar totala läsgenomströmning per executor. Den här parametern kan användas som ett övre begränsa för ditt spark-jobb dataflöde och baseras på det etablerade dataflödet på din Cosmos DB-samling.  |
| Spark.cassandra.Output.batch.Grouping.Buffer.size |  1000  | Anger antalet batchar per enskild spark-aktivitet som kan lagras i minnet innan du skickar till Cassandra-API |
| Spark.cassandra.connection.keep_alive_ms | 60000 | Definierar tidsperioden då oanvända anslutningar som är tillgängliga. | 

Justera dataflödet och graden av parallellitet av parametrarna baserat på arbetsbelastningen som du förväntar dig för ditt spark-jobb och dataflöde som du har etablerat för Cosmos DB-kontot.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Ansluter till Azure Cosmos DB Cassandra-API från Spark

### <a name="cqlsh"></a>cqlsh
Följande kommandon redogör för hur du ansluter till Azure cosmos DB Cassandra-API från cqlsh.  Detta är användbart för verifiering som du kör till exempel i Spark.<br>
**Från Mac/Linux/Unix:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.windows-ppe.net 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
Artikeln nedan beskriver Azure Databricks-kluster etablering, klusterkonfiguration för att ansluta till Azure Cosmos DB Cassandra API och flera exempelanteckningsböcker som täcker DDL-åtgärder, DML-operationer och mycket mer.<BR>
[Arbeta med Azure Cosmos DB Cassandra-API från Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  Azure HDInsight Spark
Artikeln nedan beskriver HDinsight Spark-tjänst, etablering, klusterkonfiguration för att ansluta till Azure Cosmos DB Cassandra API och flera exempelanteckningsböcker som täcker DDL-åtgärder, DML-operationer och mycket mer.<BR>
[Arbeta med Azure Cosmos DB Cassandra-API från Azure HDInsight Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Spark-miljö i allmänhet
När ovan hade specifika för Azure Spark-baserad PaaS-tjänster, beskriver det här avsnittet några allmänna Spark-miljö.  Connector beroenden, importerar och Spark-sessionskonfiguration beskrivs nedan. ”Nästa steg”-avsnittet beskriver kodexempel för DDL-åtgärder, DML-operationer och mycket mer.  

#### <a name="connector-dependencies"></a>Beroenden för anslutningen:

1. Lägg till maven-koordinater för att hämta den [Cassandra connector för Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Lägg till maven-koordinaterna för det [Azure Cosmos DB hjälpbibliotek](cassandra-spark-generic.md#dependencies-for-connectivity) för Cassandra-API

#### <a name="imports"></a>Import:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Konfiguration av Spark-session:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Nästa steg

I följande artiklar visar Spark-integrering med Azure Cosmos DB Cassandra-API. 
 
* [DDL-åtgärder](cassandra-spark-ddl-ops.md)
* [Skapa/Infoga-åtgärder](cassandra-spark-create-ops.md)
* [läsåtgärder](cassandra-spark-read-ops.md)
* [Upsert åtgärder](cassandra-spark-upsert-ops.md)
* [Borttagningsåtgärder](cassandra-spark-delete-ops.md)
* [Aggregeringsåtgärder](cassandra-spark-aggregation-ops.md)
* [Tabellen kopieringsåtgärder](cassandra-spark-table-copy-ops.md)
