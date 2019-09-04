---
title: Hämta Azure Cosmos DB mått från Azure Monitor
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 762c910336fa2b50a46eda23cf66d8a7aa383c52
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241228"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Övervaka och felsöka Azure Cosmos DB mått från Azure Monitor

Du kan visa Azure Cosmos DB mått från Azure Monitor API. Azure Monitor ger dig flera olika sätt att interagera med mått, inklusive Azure Portal, komma åt dem via REST API eller genom att fråga dem med PowerShell eller CLI. Azure Cosmos DBs mått är numeriska värden med låg latens som standard, som samlas in på en minut som standard, så du kan också aggregera dessa mått. De här måtten kan stödja real tids scenarier.  

I den här artikeln beskrivs olika Azure Cosmos DB mått som du kan visa från Azure Monitor med Azure Portal. Om du är intresse rad av vanliga användnings fall och hur Azure Cosmos DB mått som används för att analysera och felsöka problemen, se [övervaka och Felsök med mått i Azure Cosmos DB](use-metrics.md) artikeln. Du kommer att använda ett av dina befintliga Azure Cosmos-konton och se de olika måtten för databas, behållare, region, begäran eller åtgärds nivåer. Se därför till att du har ett Azure Cosmos-konto med exempel data och utför CRUD-åtgärder på dessa data.

## <a name="view-metrics-from-azure-portal"></a>Visa mått från Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **övervaka** i det vänstra navigerings fältet och välj **mått**.

   ![Mått fönstret i Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. I fönstret **mått** > väljer du **en resurs** > väljer den nödvändiga **prenumerationen**och **resurs gruppen**. För **resurs typen**väljer du **Azure Cosmos DB konton**, väljer något av dina befintliga Azure Cosmos-konton och väljer **Använd**. 

   ![Välj ett Cosmos DB konto om du vill visa mått](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Sedan kan du välja ett mått i listan över tillgängliga mått. Du kan välja mått som är speciella för att begära enheter, lagring, svars tid, tillgänglighet, Cassandra och andra. Mer information om alla tillgängliga mått i den här listan finns i avsnittet [mått per kategori](#metrics-by-category) i den här artikeln. I det här exemplet ska vi välja **enheter för programbegäran** och **AVG** som agg regerings värde. 

   Förutom dessa uppgifter kan du också välja **tidsintervallet** och **tids kornig het** för måtten. Som Max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på ditt filter. Du kan se det genomsnittliga antalet förbrukade enheter för programbegäran per minut för den valda perioden.  

   ![Välj ett mått från Azure Portal](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Lägg till filter till mått

Du kan också filtrera mått och diagrammet som visas av en speciell **samlings**-, **databasename**-, **OperationType**-, **region**-och **StatusCode**-värde. Om du vill filtrera måtten väljer du **Lägg till filter** och väljer önskad egenskap som **OperationType** och väljer ett värde, till exempel **fråga**. Diagrammet visar sedan de enheter för programbegäran som för bruk ATS för den valda perioden. De åtgärder som utförs via den lagrade proceduren loggas inte, så de är inte tillgängliga i OperationType-måttet.

![Lägg till ett filter för att välja mått kornig het](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Du kan gruppera mått med hjälp av alternativet **Använd delning** . Du kan till exempel gruppera enheter för programbegäran per åtgärds typ och Visa grafen för alla åtgärder samtidigt som visas i följande bild: 

![Lägg till Använd delnings filter](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Mått per kategori

### <a name="request-metrics"></a>Begäran-mått
            
|Mått (måttets visnings namn)|Enhet (agg regerings typ) |Beskrivning|Dimensioner| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| TotalRequests (totalt antal förfrågningar) | Antal (antal) | Antal begär Anden som gjorts| DatabaseName, samlings namn, region, StatusCode| Alla | TotalRequests, http-2xx, http-3xx, http 400, HTTP 401, internt Server fel, tjänsten är inte tillgänglig, begränsade begär Anden, genomsnittliga begär Anden per sekund | Används för att övervaka begär Anden per status kod, behållare på en minuts kornig het. Om du vill få genomsnittlig begär Anden per sekund använder du Count-aggregering på minut och dividerar med 60. |
| MetadataRequests (begär Anden om metadata) |Antal (antal) | Antal metadata-begäranden. Azure Cosmos DB underhåller systemets metadata-behållare för varje konto, så att du kan räkna upp samlingar, databaser osv. och deras konfigurationer kostnads fritt. | DatabaseName, samlings namn, region, StatusCode| Alla| |Används för att övervaka begränsningar på grund av metadata-begäranden.|
| MongoRequests (Mongo-begäranden) | Antal (antal) | Antal Mongo-begäranden som gjorts | DatabaseName, samlings namn, region, CommandName, ErrorCode| Alla |Mongo för begäran, Mongo uppdaterings frekvens, Mongo ta bort begär ande frekvens, Mongo infoga begär ande frekvens, antal Mongo för begäran| Används för att övervaka Mongo begär fel, användningar per kommando typ. |

### <a name="request-unit-metrics"></a>Enhets mått för begäran

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Request Charge) | Antal (totalt) |Mongo enheter för förbrukad begäran| DatabaseName, samlings namn, region, CommandName, ErrorCode| Alla |Mongo begär ande avgift, Mongo uppdaterings avgift, Mongo ta bort begär ande avgift, Mongo infoga begär ande avgift, antal Mongo avgift för begäran| Används för att övervaka Mongo Resource ru: er på en minut.|
| TotalRequestUnits (totalt antal enheter för programbegäran)| Antal (totalt) | Förbrukade enheter för begär Ande| DatabaseName, samlings namn, region, StatusCode |Alla| TotalRequestUnits| Används för att övervaka total användning av RU med en minuts kornig het. Använd total agg regering vid minut och dividera med 60 för att få Genomsnittligt antal RU-förbrukade per sekund.|
| ProvisionedThroughput (tillhandahållet data flöde)| Antal (max) |Tillhandahållet data flöde vid behållar kornig het| DatabaseName, ContainerName| 5 M| | Används för att övervaka etablerade data flöden per behållare.|

### <a name="storage-metrics"></a>Lagrings mått

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (tillgängligt lagrings utrymme) |Byte (totalt) | Totalt tillgängligt lagrings utrymme som har rapporter ATS enligt 5 minuters kornig het per region| DatabaseName, samlings region| 5 M| Tillgängligt lagrings utrymme| Används för att övervaka tillgänglig lagrings kapacitet (gäller endast för fasta lagrings samlingar) minimal kornig het är 5 minuter.| 
| DataUsage (data användning) |Byte (totalt) |Total data användning rapporterad enligt 5-minuters kornig het per region| DatabaseName, samlings region| 5 M |Datastorlek | Används för att övervaka total data användning i behållare och region, minsta kornig het är 5 minuter.|
| IndexUsage (index användning) | Byte (totalt) |Total användning av index rapporterad enligt 5 minuters kornig het per region| DatabaseName, samlings region| 5 M| Indexstorlek| Används för att övervaka total data användning i behållare och region, minsta kornig het är 5 minuter. |
| DocumentQuota (dokument kvot) | Byte (totalt) | Total lagrings kvot som rapporter ATS med 5 minuters kornig het per region.| DatabaseName, samlings region| 5 M |Lagringskapacitet| Används för att övervaka den totala kvoten vid container och region, minimal kornig het är 5 minuter.|
| DocumentCount (antal dokument) | Antal (totalt) |Totalt antal dokument som rapporter ATS enligt 5 minuters kornig het per region| DatabaseName, samlings region| 5 M |Antal dokument|Används för att övervaka antalet dokument i container och region, minimal kornig het är 5 minuter.|

### <a name="latency-metrics"></a>Svars tids mått

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Användning |
|---|---|---|---| ---| ---|
| ReplicationLatency (replikeringsfördröjning)| Millisekunder (lägsta, högsta, genomsnitt) | P99 för replikering i käll-och mål regioner för geo-aktiverat konto| SourceRegion, TargetRegion| Alla | Används för att övervaka P99 för replikering mellan två regioner för ett geo-replikerat konto. |


### <a name="availability-metrics"></a>Tillgänglighets mått

|Mått (måttets visnings namn) |Enhet (agg regerings typ)|Beskrivning| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---|
| ServiceAvailability (tjänst tillgänglighet)| Procent (minimum, max) | Konto begär tillgänglighet med en timkostnad| 1H | Tjänst tillgänglighet | Visar procent andelen slutförda begär Anden som skickats. En begäran anses vara misslyckad på grund av systemfel om status koden är 410, 500 eller 503 som används för att övervaka tillgängligheten för kontot med tids kornig het. |


### <a name="cassandra-api-metrics"></a>API för Cassandra mått

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Användning |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra-begäranden) | Antal (antal) | Antal API för Cassandra begär Anden som gjorts| DatabaseName, samlings namn, ErrorCode, region, OperationType, ResourceType| Alla| Används för att övervaka Cassandra-begäranden på en minuts kornig het. Om du vill få genomsnittlig begär Anden per sekund använder du Count-aggregering på minut och dividerar med 60.|
| CassandraRequestCharges (Cassandra Request avgifter) | Count (sum, min, Max, AVG) | Enheter för programbegäran som förbrukas av API för Cassandra begär Anden| DatabaseName, samlings namn, region, OperationType, ResourceType| Alla| Används för att övervaka ru: er som används per minut av ett API för Cassandra-konto.|
| CassandraConnectionClosures (Cassandra Connections-stängningar) |Antal (antal) |Antal stängda Cassandra-anslutningar| ClosureReason, region| Alla | Används för att övervaka anslutningen mellan klienter och Azure Cosmos DB API för Cassandra.|

## <a name="next-steps"></a>Nästa steg

* [Visa och övervaka mått från fönstret Azure Cosmos DB konto mått](use-metrics.md)

* [Diagnostisk loggning i Azure Cosmos DB](logging.md)
