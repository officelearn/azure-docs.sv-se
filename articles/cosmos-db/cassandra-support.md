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
ms.openlocfilehash: 53c71afc38e7b122a0ae1d066460b8df91132963
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73152256"
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
  


## <a name="cassandra-api-limits"></a>API för Cassandra gränser

Det finns inga begränsningar i Azure Cosmos DB Cassandra API för storleken på data som lagras i en tabell. Hundratals terabyte eller petabyte data kan lagras samtidigt om partitionsnyckelgränserna respekteras. Samma sak som varje entitet eller rad motsvarighet har inte några begränsningar för antalet kolumner, men den totala storleken på entiteten bör inte överstiga 2 MB. Data per partitionsnyckel får inte överstiga 10 GB som i alla andra API: er.

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

* SKAPA ett disk utrymme (replikeringsinställningarna för det här kommandot ignoreras)
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* VÄLJ 
* UPPDATERING 
* BATCH – endast ej loggade kommandon stöds 
* DELETE

Alla CRUD-åtgärder när de körs via CQLV4-kompatibla SDK returnerar extra information om felet, förbrukade enheter. Ta bort och uppdatera kommandon måste hanteras med resurs styrning för att säkerställa att det etablerade data flödet får rätt användning. 
* Observera att gc_grace_seconds-värdet måste vara noll om det anges.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Mappning av konsekvens 

Azure Cosmos DB Cassandra-API innehåller val av konsekvens för läsåtgärder.  Konsekvens mappningen beskrivs [här](consistency-levels-across-apis.md#cassandra-mapping).

## <a name="permission-and-role-management"></a>Behörighets- och rollhantering

Azure Cosmos DB stöder rollbaserad åtkomst kontroll (RBAC) för etablering, rotation av nycklar, visning av mått och skriv-och skrivskyddade lösen ord/nycklar som kan hämtas via [Azure Portal](https://portal.azure.com). Azure Cosmos DB stöder inte roller för CRUD-aktiviteter.

## <a name="keyspace-and-table-options"></a>Alternativ för tecken utrymme och tabell

Alternativen för region namn, klass, replication_factor och data Center i kommandot "skapa inloggnings utrymme" ignoreras för närvarande. Systemet använder den underliggande Azure Cosmos DBens [globala distributions](global-dist-under-the-hood.md) Metod för att lägga till regionerna. Om du behöver data över flera regioner kan du aktivera det på konto nivå med PowerShell, CLI eller portal för mer information. mer information finns i artikeln [så här lägger](how-to-manage-database-account.md#addremove-regions-from-your-database-account) du till regioner. Durable_writes kan inte inaktive ras eftersom Azure Cosmos DB säkerställer att varje skrivning är varaktig. I varje region replikerar Azure Cosmos DB data över replik uppsättningen som består av 4 repliker och den här replik uppsättnings [konfigurationen](global-dist-under-the-hood.md) kan inte ändras.
 
Alla alternativ ignoreras när tabellen skapas, förutom gc_grace_seconds som ska anges till noll.
Det finns ett extra alternativ med namnet "cosmosdb_provisioned_throughput" med det minsta värdet på 400 RU/s. Genom strömningen med hjälp av det här flödet kan du dela data flöde över flera tabeller och det är användbart för scenarier när alla tabeller inte använder det etablerade data flödet. Alter Table-kommandot tillåter ändring av det etablerade data flödet i regionerna. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Användning av anslutnings princip för Cassandra-försök

Azure Cosmos DB är ett resurs styrt system. Det innebär att du kan utföra ett visst antal åtgärder i en viss sekund baserat på de enheter för enheter som används av åtgärderna. Om ett program överskrider den gränsen inom en viss sekund kommer begär Anden att bli avgiftsbelagda och undantag kommer att genereras. API för Cassandra i Azure Cosmos DB översätter dessa undantag till överbelastade fel på det inbyggda Cassandra-protokollet. För att säkerställa att ditt program kan fånga upp och försöka utföra begär anden i fall begränsningen, [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) -och [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) -tilläggen tillhandahålls. Om du använder andra SDK: er för att komma åt API för Cassandra i Azure Cosmos DB skapar du en anslutnings princip för att försöka igen med dessa undantag.

## <a name="next-steps"></a>Nästa steg

- Kom igång med [skapa ett Cassandra API-konto, databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program

