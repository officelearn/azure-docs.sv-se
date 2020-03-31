---
title: Apache Cassandra-funktioner som stöds av Azure Cosmos DB Cassandra-API
description: Lär dig om stöd för Apache Cassandra-funktionen i Azure Cosmos DB Cassandra-API
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 0a2ace3f73379cff0b9289a8cebb10cb7930348d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240242"
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
* boolean  
* räknare  
* date  
* decimal  
* double  
* float  
* frusen  
* inet  
* int  
* lista  
* set  
* smallint  
* text  
* time  
* timestamp  
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
* Mängdfunktioner
  * min, max, avg, räkna
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
  


## <a name="cassandra-api-limits"></a>Begränsningar i API:et för Cassandra

Det finns inga begränsningar i Azure Cosmos DB Cassandra API för storleken på data som lagras i en tabell. Hundratals terabyte eller petabyte data kan lagras samtidigt om partitionsnyckelgränserna respekteras. På samma sätt har varje entitet eller radmotsvarighet inga gränser för antalet kolumner. Den totala storleken på entiteten bör dock inte överstiga 2 MB. Data per partitionsnyckel får inte överstiga 20 GB som i alla andra API:er.

## <a name="tools"></a>Verktyg 

Azure Cosmos DB Cassandra API är en hanterad tjänst-plattform. Det krävs inga hanteringskostnader eller verktyg som skräpinsamlare, Java Virtual Machine (JVM) eller nodverktyg för att hantera klustret. Det stöder verktyg som cqlsh som använder binär CQLv4-kompatibilitet. 

* Azure-portalens datautforskare, mått, loggdiagnostik, PowerShell och CLI är andra mekanismer som stöds för att hantera kontot.

## <a name="cql-shell"></a>CQL-gränssnittet  

CQLSH kommandoradsverktyget levereras med Apache Cassandra 3.1.1 och fungerar orutant genom att ställa in vissa miljövariabler.

**Windows:**

Om du använder windows rekommenderar vi att du aktiverar [Windows-filsystemet för Linux](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Du kan sedan följa Linux-kommandon nedan.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

## <a name="cql-commands"></a>CQL-kommandon

Azure Cosmos DB stöder följande databaskommandon på alla Cassandra API-konton.

* SKAPA KEYSPACE (Replikeringsinställningarna för det här kommandot ignoreras)
* CREATE TABLE 
* SKAPA INDEX (utan att ange indexnamn och fullständiga frysta index stöds ännu inte)
* TILLÅT FILTRERING
* ALTER TABLE 
* USE 
* INSERT 
* VÄLJ 
* UPDATE 
* BATCH – endast ej loggade kommandon stöds 
* DELETE

Alla CRUD-åtgärder som körs via en CQL v4-kompatibel SDK returnerar extra information om fel- och begärandenheter som förbrukas. Kommandona DELETE och UPDATE bör hanteras med resursstyrning beaktas för att säkerställa den mest effektiva användningen av det etablerade dataflödet.

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

Azure Cosmos DB Cassandra-API innehåller val av konsekvens för läsåtgärder.  Konsekvensmappningen beskrivs [här](consistency-levels-across-apis.md#cassandra-mapping).

## <a name="permission-and-role-management"></a>Behörighets- och rollhantering

Azure Cosmos DB stöder rollbaserad åtkomstkontroll (RBAC) för etablering, roterande nycklar, visningsmått och skrivskyddade och skrivskyddade lösenord/nycklar som kan erhållas via [Azure-portalen](https://portal.azure.com). Azure Cosmos DB stöder inte roller för CRUD-aktiviteter.

## <a name="keyspace-and-table-options"></a>Alternativ för nyckelutrymme och tabell

Alternativen för regionnamn, klass, replication_factor och datacenter i kommandot "Skapa keyspace" ignoreras för närvarande. Systemet använder den underliggande Azure Cosmos [DB:s globala](global-dist-under-the-hood.md) distributionsreplikeringsmetod för att lägga till regionerna. Om du behöver förekomsten av data över flera regioner kan du aktivera den på kontonivå med PowerShell, CLI eller portalen om du vill veta mer, se [hur du lägger till](how-to-manage-database-account.md#addremove-regions-from-your-database-account) regionartikeln. Durable_writes kan inte inaktiveras eftersom Azure Cosmos DB ser till att varje skrivning är hållbar. I varje region replikerar Azure Cosmos DB data över replikuppsättningen som består av fyra repliker och den här [replikuppsättningskonfigurationen](global-dist-under-the-hood.md) kan inte ändras.
 
Alla alternativ ignoreras när tabellen skapas, förutom gc_grace_seconds, som ska anges till noll.
Keyspace och tabellen har ett extra alternativ som heter "cosmosdb_provisioned_throughput" med ett minimivärde på 400 RU/s. Keyspace-dataflödet gör det möjligt att dela dataflöde över flera tabeller och det är användbart för scenarier när alla tabeller inte använder det etablerade dataflödet. Kommandot Ändra tabell gör det möjligt att ändra det etablerade dataflödet i regionerna. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Användning av principen för nytt anslutningsförsök i Cassandra

Azure Cosmos DB är ett resursstyrt system. Det innebär att du kan göra ett visst antal åtgärder i en viss sekund baserat på de begärandeenheter som förbrukas av operationerna. Om ett program överskrider den gränsen i en viss sekund är begäranden räntebegränsade och undantag kommer att genereras. Cassandra-API:et i Azure Cosmos DB översätter dessa undantag till överbelastade fel i Cassandra-inbyggda protokollet. För att säkerställa att ditt program kan avlyssna och försöka igen begäranden i fall hastighetsbegränsning, gnistan [och](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) [Java-tilläggen](https://github.com/Azure/azure-cosmos-cassandra-extensions) tillhandahålls. Om du använder andra SDK:er för att komma åt Cassandra API i Azure Cosmos DB skapar du en anslutningsprincip för att försöka igen på dessa undantag.

## <a name="next-steps"></a>Nästa steg

- Kom igång med [skapa ett Cassandra API-konto, databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program

