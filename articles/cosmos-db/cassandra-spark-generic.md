---
title: Arbeta med Azure Cosmos DB Cassandra API från Spark
description: Denna artikel är huvudsidan för Cosmos DB Cassandra API integration från Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: cb34ea44c069f067d13a6480531a94a1a515f380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70241243"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Ansluta till Azure Cosmos DB Cassandra-API:et från Spark

Den här artikeln är en av en serie artiklar om Azure Cosmos DB Cassandra API-integrering från Spark. Artiklarna omfattar anslutning, DDL-åtgärder (Data Definition Language(DDL), grundläggande DML-åtgärder (Data Manipulation Language) och avancerad Azure Cosmos DB Cassandra API-integrering från Spark. 

## <a name="prerequisites"></a>Krav
* [Etablera ett Azure Cosmos DB Cassandra API-konto.](create-cassandra-dotnet.md#create-a-database-account)

* Etablera ditt val av Spark-miljö [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Andra].

## <a name="dependencies-for-connectivity"></a>Beroenden för anslutning
* **Spark-kontakt för Cassandra:** Spark-anslutning används för att ansluta till Azure Cosmos DB Cassandra API.  Identifiera och använd den version av kontakten som finns i [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) som är kompatibel med Spark- och Scala-versionerna av din Spark-miljö.

* **Azure Cosmos DB-hjälpbibliotek för Cassandra API:** Förutom Spark-kopplingen behöver du ett annat bibliotek som heter [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) från Azure Cosmos DB. Det här biblioteket innehåller anpassade anslutningsfabriks- och återförsöksprincipklasser.

  Principen för återförsök i Azure Cosmos DB är konfigurerad för att hantera HTTP-statuskod 429("Request Rate Large") undantag. Azure Cosmos DB Cassandra API översätter dessa undantag till överbelastade fel på Cassandra native-protokollet och du kan försöka igen med back-offs. Eftersom Azure Cosmos DB använder etablerad dataflödesmodell uppstår begärandehastighetsbegränsningsundningar när ingångs-/utgående hastighetshastigheten ökar. Återförsöksprincipen skyddar dina spark-jobb mot datatoppar som tillfälligt överskrider det dataflöde som allokerats för din behållare.

  > [!NOTE] 
  > Återförsöksprincipen kan skydda dina spark-jobb mot endast tillfälliga spikar. Om du inte har konfigurerat tillräckligt många ru:er som krävs för att köra arbetsbelastningen är principen för återförsök inte tillämplig och principklassen retry rethrows undantaget.

* **Information om azure cosmos DB-kontoanslutning:** Ditt Azure Cassandra API-kontonamn, kontoslutpunkt och nyckel.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Konfigurationsparametrar för sparkanslutningsgenomströmning

I följande tabell visas Azure Cosmos DB Cassandra API-specifika konfigurationsparametrar för dataflöde som tillhandahålls av kopplingen. En detaljerad lista över alla konfigurationsparametrar finns på [konfigurationsreferenssidan](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) i Spark Cassandra Connector GitHub-databasen.

| **Egenskapsnamn** | **Standardvärde** | **Beskrivning** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Antal rader per enskild batch. Ställ in den här parametern på 1. Den här parametern används för att uppnå högre dataflöde för tunga arbetsbelastningar. |
| spark.cassandra.connection.connections_per_executor_max  | Inget | Maximalt antal anslutningar per nod per executor. 10*n motsvarar 10 anslutningar per nod i ett n-nod Cassandra-kluster. Så, om du behöver 5 anslutningar per nod per executor för en 5 nod Cassandra kluster, bör du ställa in den här konfigurationen till 25. Ändra det här värdet baserat på graden av parallellitet eller antalet utförare som dina spark-jobb har konfigurerats för.   |
| spark.cassandra.output.concurrent.writes  |  100 | Definierar antalet parallella skrivningar som kan inträffa per utförare. Eftersom du anger "batch.size.rows" till 1, se till att skala upp det här värdet därefter. Ändra det här värdet baserat på graden av parallellitet eller det dataflöde som du vill uppnå för din arbetsbelastning. |
| spark.cassandra.concurrent.reads |  512 | Definierar antalet parallella läsningar som kan förekomma per utförare. Ändra det här värdet baserat på graden av parallellitet eller det dataflöde som du vill uppnå för din arbetsbelastning  |
| spark.cassandra.output.throughput_mb_per_sec  | Inget | Definierar det totala skrivflödet per utförare. Den här parametern kan användas som en övre gräns för ditt spark-jobbdataflöde och basera det på det etablerade dataflödet för Cosmos-behållaren.   |
| spark.cassandra.input.reads_per_sec| Inget   | Definierar det totala läsflödet per utförare. Den här parametern kan användas som en övre gräns för ditt spark-jobbdataflöde och basera det på det etablerade dataflödet för Cosmos-behållaren.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Definierar antalet batchar per enskild spark-uppgift som kan lagras i minnet innan du skickar till Cassandra API |
| spark.cassandra.connection.keep_alive_ms | 60000 | Definierar den tidsperiod fram till vilken oanvända anslutningar är tillgängliga. | 

Justera dataflödet och graden av parallellitet för dessa parametrar baserat på den arbetsbelastning du förväntar dig för dina spark-jobb och det dataflöde som du har etablerat för ditt Cosmos DB-konto.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Ansluta till Azure Cosmos DB Cassandra API från Spark

### <a name="cqlsh"></a>cqlsh (cqlsh)
Följande kommandon beskriver hur du ansluter till Azure CosmosDB Cassandra API från cqlsh.  Detta är användbart för validering när du kör igenom exemplen i Spark.<br>
**Från Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure-databricks
Artikeln nedan omfattar Azure Databricks klusteretablering, klusterkonfiguration för anslutning till Azure Cosmos DB Cassandra API och flera exempeldatorböcker som täcker DDL-åtgärder, DML-åtgärder med mera.<BR>
[Arbeta med Azure Cosmos DB Cassandra API från Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure HDInsight-Spark
Artikeln nedan omfattar HDinsight-Spark-tjänsten, etablering, klusterkonfiguration för anslutning till Azure Cosmos DB Cassandra API och flera exempeldatorböcker som täcker DDL-åtgärder, DML-åtgärder med mera.<BR>
[Arbeta med Azure Cosmos DB Cassandra API från Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. Gnista miljö i allmänhet
Avsnitten ovan var specifika för Azure Spark-baserade PaaS-tjänster, men det här avsnittet täcker alla allmänna Spark-miljöer.  Anslutningsberoenden, importer och Spark-sessionskonfiguration beskrivs nedan. Avsnittet "Nästa steg" omfattar kodexempel för DDL-åtgärder, DML-åtgärder med mera.  

#### <a name="connector-dependencies"></a>Anslutningsberoenden:

1. Lägg till maven-koordinaterna för att hämta [Cassandra-kontakten för Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Lägga till maven-koordinaterna för [Azure Cosmos DB-hjälpbiblioteket](cassandra-spark-generic.md#dependencies-for-connectivity) för Cassandra API

#### <a name="imports"></a>Import:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Konfiguration av Spark-sessions:

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

Följande artiklar demonstrerar Spark-integrering med Azure Cosmos DB Cassandra API. 
 
* [DDL-operationer](cassandra-spark-ddl-ops.md)
* [Skapa/infoga åtgärder](cassandra-spark-create-ops.md)
* [Läs operationer](cassandra-spark-read-ops.md)
* [Upsert-operationer](cassandra-spark-upsert-ops.md)
* [Ta bort åtgärder](cassandra-spark-delete-ops.md)
* [Sammansättningsåtgärder](cassandra-spark-aggregation-ops.md)
* [Tabellkopieringsåtgärder](cassandra-spark-table-copy-ops.md)
