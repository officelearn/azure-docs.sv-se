---
title: Azure Cosmos DB referens för övervaknings data | Microsoft Docs
description: Logg och statistik referens för övervakning av data från Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 5f46c9bf53d791eaec68763edc32996847e78ebd
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186908"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB övervaknings data referens
Den här artikeln innehåller en referens till logg-och mät data som samlas in för att analysera prestanda och tillgänglighet för Azure Cosmos DB. Mer information om hur du samlar in och analyserar övervaknings data för Azure Cosmos DB finns i [övervaknings Cosmos DB](monitor-cosmos-db.md) .


## <a name="resource-logs"></a>Resursloggar
I följande tabell visas egenskaperna för Azure Cosmos DB resurs loggar när de samlas in i Azure Monitor loggar eller Azure Storage. I Azure Monitor loggar samlas de in i tabellen **AzureDiagnostics** med **ResourceProvider** -värdet *Microsoft. DOCUMENTDB*.

| Azure Storage-fält eller någon egenskap | Azure Monitor loggar egenskap | Beskrivning |
| --- | --- | --- |
| **tid** | **TimeGenerated** | Datum och tid (UTC) när åtgärden utfördes. |
| **Resurs-ID** | **Resurs** | Azure Cosmos DB-kontot som loggar är aktiverad.|
| **Kategori** | **Kategori** | För Azure Cosmos DB-loggar är **DataPlaneRequests** är endast tillgängligt värde. |
| **OperationName** | **OperationName** | Åtgärdens namn. Det här värdet kan vara något av följande åtgärder: skapa, uppdatera, Läs, ReadFeed, ta bort, Ersätt, kör, SQL-fråga, fråga, JSQuery, Head, HeadFeed eller Upsert.   |
| **Egenskaper** | Saknas | Innehållet i det här fältet beskrivs i de rader som följer. |
| **Aktivitets-ID** | **activityId_g** | Unikt GUID för den loggade åtgärden. |
| **UserAgent** | **userAgent_s** | En sträng som anger klientanvändaragent som utför förfrågan. Formatet är {användarnamn för agenten} / {version}.|
| **requestResourceType** | **requestResourceType_s** | Typ av resurs som används. Det här värdet kan vara något av följande resurstyper: databasen, behållare, dokument, bifogad fil, användare, behörighet, StoredProcedure, utlösare, UserDefinedFunction eller erbjudandet. |
| **statusCode** | **statusCode_s** | Svarsstatus för åtgärden. |
| **requestResourceId** | **Resurs-ID** | ResourceId som gäller för begäran. Värdet kan peka databaseRid, collectionRid eller documentRid beroende på de åtgärder som utförs.|
| **clientIpAddress** | **clientIpAddress_s** | Klientens IP-adress. |
| **requestCharge** | **requestCharge_s** | Antalet enheter för programbegäran som används av åtgärden |
| **collectionRid** | **collectionId_s** | Unikt ID för samlingen.|
| **Varaktighet** | **duration_s** | Åtgärdens varaktighet i millisekunder. |
| **requestLength** | **requestLength_s** | Längden på begäran, i byte. |
| **responseLength** | **responseLength_s** | Längden på svaret, i byte.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Det här värdet är inte tom när [resurstokens](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) används för autentisering. Värdet som pekar på resurs-ID för användaren. |

## <a name="metrics"></a>Mått
I följande tabeller visas de plattforms mått som samlats in för Azure CosmOS DB. Alla mått lagras i namn området **Cosmos DB standard mått**.

#### <a name="request-metrics"></a>Begäran-mått
            
|Mått (måttets visnings namn)|Enhet (agg regerings typ) |Beskrivning|Dimensioner| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| TotalRequests (totalt antal förfrågningar) | Antal (antal) | Antal begär Anden som gjorts| DatabaseName, samlings namn, region, StatusCode| Alla | TotalRequests, http-2xx, http-3xx, http 400, HTTP 401, internt Server fel, tjänsten är inte tillgänglig, begränsade begär Anden, genomsnittliga begär Anden per sekund | Används för att övervaka begär Anden per status kod, behållare på en minuts kornig het. Om du vill få genomsnittlig begär Anden per sekund använder du Count-aggregering på minut och dividerar med 60. |
| MetadataRequests (begär Anden om metadata) |Antal (antal) | Antal metadata-begäranden. Azure Cosmos DB underhåller systemets metadata-behållare för varje konto, så att du kan räkna upp samlingar, databaser osv. och deras konfigurationer kostnads fritt. | DatabaseName, samlings namn, region, StatusCode| Alla| |Används för att övervaka begränsningar på grund av metadata-begäranden.|
| MongoRequests (Mongo-begäranden) | Antal (antal) | Antal Mongo-begäranden som gjorts | DatabaseName, samlings namn, region, CommandName, ErrorCode| Alla |Mongo för begäran, Mongo uppdaterings frekvens, Mongo ta bort begär ande frekvens, Mongo infoga begär ande frekvens, antal Mongo för begäran| Används för att övervaka Mongo begär fel, användningar per kommando typ. |

#### <a name="request-unit-metrics"></a>Enhets mått för begäran

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Request Charge) | Antal (totalt) |Mongo enheter för förbrukad begäran| DatabaseName, samlings namn, region, CommandName, ErrorCode| Alla |Mongo begär ande avgift, Mongo uppdaterings avgift, Mongo ta bort begär ande avgift, Mongo infoga begär ande avgift, antal Mongo avgift för begäran| Används för att övervaka Mongo Resource ru: er på en minut.|
| TotalRequestUnits (totalt antal enheter för programbegäran)| Antal (totalt) | Förbrukade enheter för begär Ande| DatabaseName, samlings namn, region, StatusCode |Alla| TotalRequestUnits| Används för att övervaka total användning av RU med en minuts kornig het. Använd total agg regering vid minut och dividera med 60 för att få Genomsnittligt antal RU-förbrukade per sekund.|
| ProvisionedThroughput (tillhandahållet data flöde)| Antal (max) |Tillhandahållet data flöde vid behållar kornig het| DatabaseName, ContainerName| 5 M| | Används för att övervaka etablerade data flöden per behållare.|

#### <a name="storage-metrics"></a>Lagrings mått

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (tillgängligt lagrings utrymme) |Byte (totalt) | Totalt tillgängligt lagrings utrymme som har rapporter ATS enligt 5 minuters kornig het per region| DatabaseName, samlings region| 5 M| Tillgängligt lagrings utrymme| Används för att övervaka tillgänglig lagrings kapacitet (gäller endast för fasta lagrings samlingar) minimal kornig het är 5 minuter.| 
| DataUsage (data användning) |Byte (totalt) |Total data användning rapporterad enligt 5-minuters kornig het per region| DatabaseName, samlings region| 5 M |Data storlek | Används för att övervaka total data användning i behållare och region, minsta kornig het är 5 minuter.|
| IndexUsage (index användning) | Byte (totalt) |Total användning av index rapporterad enligt 5 minuters kornig het per region| DatabaseName, samlings region| 5 M| Index storlek| Används för att övervaka total data användning i behållare och region, minsta kornig het är 5 minuter. |
| DocumentQuota (dokument kvot) | Byte (totalt) | Total lagrings kvot som rapporter ATS med 5 minuters kornig het per region.| DatabaseName, samlings region| 5 M |Lagringskapacitet| Används för att övervaka den totala kvoten vid container och region, minimal kornig het är 5 minuter.|
| DocumentCount (antal dokument) | Antal (totalt) |Totalt antal dokument som rapporter ATS enligt 5 minuters kornig het per region| DatabaseName, samlings region| 5 M |Antal dokument|Används för att övervaka antalet dokument i container och region, minimal kornig het är 5 minuter.|

#### <a name="latency-metrics"></a>Svars tids mått

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Användning |
|---|---|---|---| ---| ---|
| ReplicationLatency (replikeringsfördröjning)| Millisekunder (lägsta, högsta, genomsnitt) | P99 för replikering i käll-och mål regioner för geo-aktiverat konto| SourceRegion, TargetRegion| Alla | Används för att övervaka P99 för replikering mellan två regioner för ett geo-replikerat konto. |


#### <a name="availability-metrics"></a>Tillgänglighets mått

|Mått (måttets visnings namn) |Enhet (agg regerings typ)|Beskrivning| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---|
| ServiceAvailability (tjänst tillgänglighet)| Procent (minimum, max) | Konto begär tillgänglighet med en timkostnad| 1H | Tjänst tillgänglighet | Visar procent andelen slutförda begär Anden som skickats. En begäran anses vara misslyckad på grund av systemfel om status koden är 410, 500 eller 503 som används för att övervaka tillgängligheten för kontot med tids kornig het. |


#### <a name="cassandra-api-metrics"></a>API för Cassandra mått

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Användning |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra-begäranden) | Antal (antal) | Antal API för Cassandra begär Anden som gjorts| DatabaseName, samlings namn, ErrorCode, region, OperationType, ResourceType| Alla| Används för att övervaka Cassandra-begäranden på en minuts kornig het. Om du vill få genomsnittlig begär Anden per sekund använder du Count-aggregering på minut och dividerar med 60.|
| CassandraRequestCharges (Cassandra Request avgifter) | Count (sum, min, Max, AVG) | Enheter för programbegäran som förbrukas av API för Cassandra begär Anden| DatabaseName, samlings namn, region, OperationType, ResourceType| Alla| Används för att övervaka ru: er som används per minut av ett API för Cassandra-konto.|
| CassandraConnectionClosures (Cassandra Connections-stängningar) |Antal (antal) |Antal stängda Cassandra-anslutningar| ClosureReason, region| Alla | Används för att övervaka anslutningen mellan klienter och Azure Cosmos DB API för Cassandra.|

## <a name="see-also"></a>Se även

- En beskrivning av övervaknings Azure Cosmos DB finns i [övervaknings Azure Cosmos DB](monitor-cosmos-db.md) .
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) .