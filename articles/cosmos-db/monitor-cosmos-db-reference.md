---
title: Azure Cosmos DB övervakningsdatareferens | Microsoft-dokument
description: Logg- och måttreferens för övervakning av data från Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588730"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Referens till Azure Cosmos DB-övervakningsdata
Den här artikeln är en referens till de logg- och måttdata som samlas in för att analysera prestanda och tillgänglighet för Azure Cosmos DB. Mer information om hur du samlar in och analyserar övervakningsdata för Azure Cosmos DB finns i [Övervakning av Cosmos DB.](monitor-cosmos-db.md)


## <a name="resource-logs"></a>Resursloggar
I följande tabell visas egenskaperna för Azure Cosmos DB-resursloggar när de samlas in i Azure Monitor Logs eller Azure Storage. I Azure Monitor Logs samlas de in i tabellen **AzureDiagnostics** med värdet **ResourceProvider** för *MICROSOFT. DOCUMENTDB*. 

| Azure Storage fält eller egenskap | Egenskapen Azure Monitor Logs | Beskrivning |
| --- | --- | --- |
| **Tid** | **TimeGenerated** | Datum och tid (UTC) när åtgärden inträffade. |
| **resourceId (resourceId)** | **Resurs** | Azure Cosmos DB-konto som loggar är aktiverade för.|
| **Kategori** | **Kategori** | För Azure Cosmos DB-loggar är **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** de tillgängliga loggtyperna. |
| **operationName** | **OperationNamn** | Operationens namn. Det här värdet kan vara någon av följande åtgärder: Skapa, Uppdatera, Läsa, Läsfeed, Ta bort, Ersätta, Kör, SqlQuery, Query, JSQuery, Head, HeadFeed eller Upsert.   |
| **Egenskaper** | Saknas | Innehållet i det här fältet beskrivs i de rader som följer. |
| **activityId (på)** | **activityId_g** | Det unika GUID för den loggade åtgärden. |
| **Useragent** | **userAgent_s** | En sträng som anger klientanvändaragenten som utför begäran. Formatet är {user agent name}/{version}.|
| **requestResourceType** | **requestResourceType_s** | Typen av resurs som används. Det här värdet kan vara någon av följande resurstyper: Databas, Behållare, Dokument, Bifogad fil, Användare, Behörighet, StoredProcedure, Trigger, UserDefinedFunction eller Offer. |
| **statusKoda** | **statusCode_s** | Svarsstatus för åtgärden. |
| **requestResourceId** | **ResourceId** | Den resourceId som hör till begäran. Värdet kan peka på databaseRid, collectionRid eller documentRid beroende på vilken åtgärd som utförs.|
| **clientIpAddress** | **clientIpAddress_s** | Klientens IP-adress. |
| **begärLaddning** | **requestCharge_s** | Antalet ru:er som används av åtgärden |
| **insamlingRid** | **collectionId_s** | Det unika ID:t för samlingen.|
| **Varaktighet** | **duration_s** | Varaktigheten av operationen, i millisekunder. |
| **begäran Längd** | **requestLength_s** | Längden på begäran, i byte. |
| **svarLängd** | **responseLength_s** | Längden på svaret, i byte.|
| **resursTokenUserRid** | **resourceTokenUserRid_s** | Det här värdet är inte tomt när [resurstoken](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) används för autentisering. Värdet pekar på användarens resurs-ID. |

En lista över alla Azure Monitor-loggkategorier och länkar till associerade scheman finns i [kategorier och scheman för Azure Monitor Logs](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Mått
I följande tabeller visas de plattformsmått som samlats in för Azure CosmOS DB. Alla mått lagras i **standardmåtten cosmos DB-standardvärden**för namnområdet Cosmos DB .

En lista över alla Azure Monitor-supportmått (inklusive CosmosDB) finns i [Azure Monitor-mått som stöds](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Begär mått
            
|Mått (måttvisningsnamn)|Enhet (aggregeringstyp) |Beskrivning|Dimensioner| Tidsgranulariteter| Äldre måttmappning | Användning |
|---|---|---|---| ---| ---| ---|
| Totalt Antal anbud (totalt antal begäranden) | Antal (antal) | Antal inst.| Databasnamn, samlingsnamn, region, statuskod| Alla | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Internal Server error, Service Unavailable, Throttled Requests, Average Requests per Second | Används för att övervaka begäranden per statuskod, behållare med en minuts granularitet. Om du vill få genomsnittliga begäranden per sekund använder du Antal aggregering per minut och dividerar med 60. |
| MetadataRequests (metadatabegäranden) |Antal (antal) | Antal metadatabegäranden. Azure Cosmos DB underhåller systemmetadatabehållare för varje konto, vilket gör att du kan räkna upp samlingar, databaser osv., och deras konfigurationer, kostnadsfritt. | Databasnamn, samlingsnamn, region, statuskod| Alla| |Används för att övervaka begränsningar på grund av metadatabegäranden.|
| MongoRequests (Mongo Förfrågningar) | Antal (antal) | Antal Mongo-begäranden | Databasnamn, samlingsnamn, region, kommandonamn, felkod| Alla |Mångo fråga begäran hastighet, Mongo Uppdatering begäran hastighet, Mongo Ta bort begäran hastighet, Mongo Infoga begäranhetsfrekvens, mångo räkna begäran hastighet| Används för att övervaka Mongo begäran fel, användningar per kommandotyp. |

#### <a name="request-unit-metrics"></a>Begär enhetsmått

|Mått (måttvisningsnamn)|Enhet (aggregeringstyp)|Beskrivning|Dimensioner| Tidsgranulariteter| Äldre måttmappning | Användning |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (mångo begäran avgift) | Antal (totalt) |Mongo-begärandeenheter förbrukas| Databasnamn, samlingsnamn, region, kommandonamn, felkod| Alla |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Används för att övervaka Mongo resurs RU: er i en minut.|
| Totalt Antal begärandeenheter (totalt antal begärandeenheter)| Antal (totalt) | Begär enheter som förbrukats| Databasnamn, samlingsnamn, region, statuskod |Alla| TotaltRequestUnits| Används för att övervaka total RU-användning med en minuts granularitet. Om du vill få genomsnittlig RU förbrukad per sekund använder du Total aggregering per minut och dividera med 60.|
| Etablerat Informationsflöde (etablerat dataflöde)| Antal (max) |Etablerat dataflöde vid behållarens granularitet| Databasnamn, ContainerName| 5M| | Används för att övervaka etablerat dataflöde per behållare.|

#### <a name="storage-metrics"></a>Lagringsmått

|Mått (måttvisningsnamn)|Enhet (aggregeringstyp)|Beskrivning|Dimensioner| Tidsgranulariteter| Äldre måttmappning | Användning |
|---|---|---|---| ---| ---| ---|
| TillgängligtStora (tillgängligt lagringsutrymme) |Byte (totalt) | Totalt tillgängligt lagringsutrymme rapporteras vid 5 minuters granularitet per region| Databasnamn, samlingsnamn, region| 5M| Tillgänglig lagring| Används för att övervaka tillgänglig lagringskapacitet (gäller endast för fasta lagringssamlingar) Minsta granularitet bör vara 5 minuter.| 
| Dataanvändning (dataanvändning) |Byte (totalt) |Total dataanvändning som rapporteras vid 5 minuters granularitet per region| Databasnamn, samlingsnamn, region| 5M |Datastorlek | Används för att övervaka den totala dataanvändningen i behållare och region, bör minsta granularitet vara 5 minuter.|
| IndexUsage (indexanvändning) | Byte (totalt) |Total indexanvändning som rapporteras vid 5 minuters granularitet per region| Databasnamn, samlingsnamn, region| 5M| Indexstorlek| Används för att övervaka den totala dataanvändningen i behållare och region, bör minsta granularitet vara 5 minuter. |
| DocumentQuota (dokumentkvot) | Byte (totalt) | Total lagringskvot som rapporteras vid 5 minuters granularitet per region.| Databasnamn, samlingsnamn, region| 5M |Lagringskapacitet| Används för att övervaka den totala kvoten i behållare och region, bör minsta granularitet vara 5 minuter.|
| DocumentCount (antal dokument) | Antal (totalt) |Totalt antal dokument som rapporterats vid 5 minuters granularitet per region| Databasnamn, samlingsnamn, region| 5M |Antal dokument|Används för att övervaka antalet dokument i behållare och region, bör minsta granularitet vara 5 minuter.|

#### <a name="latency-metrics"></a>Svarstidsmått

|Mått (måttvisningsnamn)|Enhet (aggregeringstyp)|Beskrivning|Dimensioner| Tidsgranulariteter| Användning |
|---|---|---|---| ---| ---|
| Replikeringssvarstid (replikeringssvarstid)| MilliSeconds (Minimum, Maximum, Average) | P99 Replikeringsfördröjning över käll- och målområden för geoaktiverat konto| Källregion, Målregion| Alla | Används för att övervaka P99-replikeringsfördröjning mellan två regioner för ett georeplikerat konto. |
| Svarstid på serversidan| MilliSeconds (genomsnitt) | Tid som servern tar för att bearbeta begäran. | CollectionName, ConnectionMode, DatabaseName, OperationType, PublicAPIType, Region | Alla | Används för att övervaka svarstiden för begäran på Azure Cosmos DB-servern. |



#### <a name="availability-metrics"></a>Tillgänglighetsmått

|Mått (måttvisningsnamn) |Enhet (aggregeringstyp)|Beskrivning| Tidsgranulariteter| Äldre måttmappning | Användning |
|---|---|---|---| ---| ---|
| ServiceTillgänglighet (tjänstens tillgänglighet)| Procent (minimum, maximum) | Kontoförfrågningar tillgänglighet på en timmes granularitet| 1H (1H) | Tjänstens tillgänglighet | Representerar procentandelen av den totala skickade begäranden. En begäran anses vara misslyckad på grund av systemfel om statuskoden är 410, 500 eller 503 Används för att övervaka kontots tillgänglighet vid timmes granularitet. |


#### <a name="cassandra-api-metrics"></a>Cassandra API-mått

|Mått (måttvisningsnamn)|Enhet (aggregeringstyp)|Beskrivning|Dimensioner| Tidsgranulariteter| Användning |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra Förfrågningar) | Antal (antal) | Antal Cassandra API-begäranden som gjorts| Databasnamn, samlingsnamn, errorcode, region, OperationsType, ResourceType| Alla| Används för att övervaka Cassandra-begäranden med en minuts granularitet. Om du vill få genomsnittliga begäranden per sekund använder du Antal aggregering per minut och dividerar med 60.|
| CassandraRequestCharges (Cassandra Begär avgifter) | Antal (Summa, Min, Max, Genomsnitt) | Begär enheter som förbrukas av Cassandra API-begäranden| Databasnamn, samlingsnamn, region, OperationsType, ResourceType| Alla| Används för att övervaka ru:er som används per minut av ett Cassandra API-konto.|
| CassandraConnectionClosures (Cassandra Anslutningsstängningar) |Antal (antal) |Antal Cassandra-anslutningar stängda| ClosureReason, Region| Alla | Används för att övervaka anslutningen mellan klienter och Azure Cosmos DB Cassandra API.|

## <a name="see-also"></a>Se även

- Se [Övervaka Azure Cosmos DB](monitor-cosmos-db.md) för en beskrivning av övervakningen av Azure Cosmos DB.
- Mer information om hur du övervakar Azure-resurser finns i [Övervaka Azure-resurser med Azure-resurser.](../azure-monitor/insights/monitor-azure-resource.md)
