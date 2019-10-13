---
title: Apache Cassandra-funktioner och -kommandon som stöds av Azure Cosmos DB Cassandra-API
description: Lär dig om stöd för Apache Cassandra-funktionen i Azure Cosmos DB Cassandra-API
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 0dcca2175d6ccc35a51bccb1e47f75d25cb8b11f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299199"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Apache Cassandra-funktioner som stöds av Azure Cosmos DB Cassandra-API 

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan kommunicera med Azure Cosmos DB Cassandra-API via Cassandra Query Language (CQL) v4[-trådprotokollkompatibla](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) Cassandra-[klientdrivrutiner](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) med öppen källkod. 

Med hjälp av Azure Cosmos DB Cassandra-API kan du utnyttja fördelarna med Apache Cassandra-API:er samt företagsfunktioner som tillhandahåller Azure Cosmos DB. Företagsfunktionerna är [global distribution](distribute-data-globally.md), [automatisk utskalning av partitionering](partition-data.md), garantier för tillgänglighet och svarstid, vilande kryptering, säkerhetskopior och mycket mer.

## <a name="cassandra-protocol"></a>Cassandra-protokollet 

Azure Cosmos DB Cassandra API är kompatibelt med CQL-version **v4**. De CQL-kommandon, verktyg, begränsningar och undantag som stöds anges nedan. Klientdrivrutiner som förstår dessa protokoll bör kunna ansluta till Cosmos DB med Azure Cosmos DB Cassandra-API:et.

## <a name="cassandra-driver"></a>Cassandra-drivrutinen

Följande versioner av Cassandra-drivrutiner stöds av Azure Cosmos DB Cassandra-API:et:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>CQL-datatyper 

Azure Cosmos DB Cassandra-API:et stöder följande CQL-datatyper:

* ascii  
* bigint  
* blob  
* boolesk  
* räknare  
* datum  
* decimal  
* double  
* flyt  
* frusen  
* inet  
* int  
* lista  
* set  
* smallint  
* text  
* time  
* tidsstämpel  
* timeuuid  
* tinyint  
* tuppel  
* uuid  
* varchar  
* varint  
* tupplar  
* udts  
* map  

## <a name="cql-functions"></a>CQL-funktioner

Azure Cosmos DB Cassandra-API:et stöder följande CQL-funktioner:

* Token  
* Mängd funktioner
  * min, Max, genomsnitt, antal
* Blobkonverteringsfunktioner 
  * typeAsBlob(value)  
  * blobAsType(value)
* UUID och timeuuid 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date) 
  


## <a name="cassandra-query-language-limits"></a>Begränsningar för Cassandra-frågespråket

Det finns inga begränsningar i Azure Cosmos DB Cassandra API för storleken på data som lagras i en tabell. Hundratals terabyte eller petabyte data kan lagras samtidigt om partitionsnyckelgränserna respekteras. På samma sätt har varje entitet eller motsvarande rad inga begränsningar för antalet kolumner, men den totala storleken på entiteten får innehålla högst 2 MB.

## <a name="tools"></a>Verktyg 

Azure Cosmos DB Cassandra API är en hanterad tjänst-plattform. Det krävs inga hanteringskostnader eller verktyg som skräpinsamlare, Java Virtual Machine (JVM) eller nodverktyg för att hantera klustret. Det stöder verktyg som cqlsh som använder binär CQLv4-kompatibilitet. 

* Azure-portals datautforskare, mått, loggdiagnostik, PowerShell och cli är andra mekanismer som stöds för att hantera kontot.

## <a name="cql-shell"></a>CQL-gränssnittet  

CQLSH-kommandoradsverktyget levereras med Apache Cassandra 3.1.1 och fungerar direkt med följande miljövariabler aktiverade:

Innan du kör följande kommandon [lägger du till ett Baltimore-rotcertifikat i arkivet cacerts](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store). 

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```
**Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```

## <a name="cql-commands"></a>CQL-kommandon

Azure Cosmos DB stöder följande databaskommandon på alla Cassandra API-konton.

* SKAPA ett tecken utrymme (replikeringsinställningarna för det här kommandot ignoreras, systemet använder underliggande [Azure Cosmos DBS replikeringspartner](global-dist-under-the-hood.md). Om du behöver data mellan regioner kan du aktivera det på konto nivå med PowerShell, CLI eller portal, och se [hur du lägger till eller tar bort regioner för din konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account) artikel.
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* VÄLJ 
* UPPDATERING 
* BATCH – endast ej loggade kommandon stöds 
* DELETE

Alla CRUD-åtgärder när de körs via CQLV4-kompatibla SDK returnerar extra information om felet, förbrukade enheter. Ta bort och uppdatera kommandon måste hanteras med resurs styrning för att undvika att det etablerade data flödet får rätt användning. 
* Observera att gc_grace_seconds-värdet måste vara noll om det anges.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mappning av konsekvens 

Azure Cosmos DB Cassandra-API innehåller val av konsekvens för läsåtgärder.  Konsekvens mappningen är detaljerad [här [(https://docs.microsoft.com/azure/cosmos-db/consistency-levels-across-apis#cassandra-mapping).

## <a name="permission-and-role-management"></a>Behörighets- och rollhantering

Azure Cosmos DB stöder rollbaserad åtkomst kontroll (RBAC) för etablering, rotation av nycklar, visning av mått och skriv-och skrivskyddade lösen ord/nycklar som kan hämtas via [Azure Portal](https://portal.azure.com). Azure Cosmos DB stöder inte roller för CRUD-aktiviteter. 

## <a name="keyspace-and-table-options"></a>Alternativ för tecken utrymme och tabell

Alternativen för region namn, klass, replication_factor, data Center i Create respace-kommandot ignoreras vid tillfället. Systemet använder underliggande Azure Cosmos DB [globala distribution](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) om du lägger till obligatoriska regioner. Om du behöver data mellan regioner kan du aktivera det på konto nivå med PowerShell, CLI eller portal, och läsa mer i det här dokumentet: https://docs.microsoft.com/en-us/azure/cosmos-db/how-to-manage-database-account#addremove-regions-from-your-database-account. Durable_writes kan inte inaktive ras eftersom Cosmos DB säkerställer att varje skrivning är varaktig. I varje region Cosmos DB replikerar data över REPLICASET som består av 4 repliker och den här REPLICASET- [konfigurationen](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) kan inte ändras. Alla alternativ för att skapa tabeller ignoreras, utom gc_grace_seconds som ska vara noll.
Ett tecken utrymme och en tabell har extra alternativ-cosmosdb_provisioned_throughput med det lägsta värdet på 400. Genom att använda genomflöde i ett flöde kan du dela data flöde i flera tabeller och användbart för scenarier när alla tabeller inte använder data flödet. Alter Table tillåter att det etablerade data flödet ändras i regionerna. Skapa sampleks med replikering = {' Class ': ' SimpleStrategy '} och cosmosdb_provisioned_throughput = 2000;  
CREATE TABLE sampleks. T1 (user_id int PRIMARY KEY, LastName text) med cosmosdb_provisioned_throughput = 2000; ALTER TABLE gks1. T1 med cosmosdb_provisioned_throughput = 10000;

## <a name="usage-of-cassandra-retry-connection-policy"></a>Användning av anslutnings princip för Cassandra-försök

Azure Cosmos DB är resurs styrt system. Detta innebär att du kan utföra ett visst antal åtgärder i en viss sekund som är begränsad av det etablerade data flödet baserat på enheter för programbegäran som används av åtgärder. Om programmet överskrider den gränsen i en specifik frekvens för begränsning av andra begär Anden kommer undantag att returneras. Cosmos DB API för Cassandra, översätter dessa undantag till överbelastade fel på det Cassandra ursprungliga protokollet. För att se till att ditt program kan fånga upp och göra ett nytt försök för hastighets begränsning kan en [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) -och [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) -hjälp tillhandahållas. Om du använder andra SDK: er för att komma åt API för Cassandra av Cosmos DB ska du skapa en anslutnings princip för att försöka hämta de här undantagen. 

## <a name="next-steps"></a>Nästa steg

- Kom igång med [skapa ett Cassandra API-konto, databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program

