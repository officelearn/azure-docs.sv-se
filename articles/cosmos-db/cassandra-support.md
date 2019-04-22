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
ms.openlocfilehash: 46eea21e1eafce1696ed1cf77a1f334798f0bc17
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848410"
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
* dubbel  
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

* CREATE KEYSPACE 
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* Välj 
* UPPDATERING 
* BATCH – endast ej loggade kommandon stöds 
* DELETE

Alla crud-åtgärder returnerar extra information om felet, förbrukade begäransenheter och aktivitets-ID när de körs via CQLV4-kompatibla SDK:er. Kommandon för borttagning och uppdatering måste hanteras med resursstyrning i åtanke för att undvika överanvändning av etablerade resurser. 
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

Azure Cosmos DB Cassandra-API innehåller val av konsekvens för läsåtgärder. Alla skrivåtgärder, oavsett kontokonsekvens, skrivs alltid med serviceavtal för skrivprestanda.

## <a name="permission-and-role-management"></a>Behörighets- och rollhantering

Azure Cosmos DB stöder rollbaserad åtkomstkontroll (RBAC) och läs- och skrivbara lösenord/nycklar som kan hämtas via [Azure-portalen](https://portal.azure.com). Azure Cosmos DB stöder ännu inte användare och roller för dataplansaktiviteter. 

## <a name="planned-support"></a>Planerad support 
* Regionnamn i kommandot create keyspace ignoreras för närvarande. Distribution av data implementeras i underliggande Cosmos DB-plattform och exponeras visa portalen eller PowerShell för kontot. 





## <a name="next-steps"></a>Nästa steg

- Kom igång med [skapa ett Cassandra API-konto, databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program

