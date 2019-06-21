---
title: Hämta Azure Cosmos DB-mått från Azure Monitor
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276800"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Övervaka och felsöka Azure Cosmos DB-mått från Azure Monitor

Du kan visa mått för Azure Cosmos DB från Azure Monitor-API. Azure Monitor innehåller flera olika sätt att interagera med mätvärden, inklusive Azure-portalen, få åtkomst till dem via REST API eller frågor till dem med PowerShell eller CLI. Azure Cosmos DB-mått med låg latens numeriska värden som samlas in med en minut frekvens som standard, du kan även aggregera de här måtten. De här måtten är kan stödja realtidsscenarion.  

Den här artikeln beskrivs olika Azure Cosmos DB mått du kan visa från Azure Monitor med hjälp av Azure portal. Om du är intresserad av i vanliga användningsfall hur Azure Cosmos DB-mått på nytt för att analysera och felsöka dessa problem och se [övervaka och felsöka med mått i Azure Cosmos DB](use-metrics.md) artikeln. Du använder ett av dina befintliga Azure-Cosmos-konton och visa olika mått på databasen, behållare, region, begäran eller åtgärden nivåer. Kontrollera därför att du har ett Azure Cosmos-konto med exempeldata och utför CRUD-åtgärder på dessa data.

## <a name="view-metrics-from-azure-portal"></a>Visa mått från Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **övervakaren** från det vänstra navigeringsfältet och väljer **mått**.

   ![Fönstret mått i Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. Från den **mått** fönstret > **väljer du en resurs** > Välj de nödvändiga **prenumeration**, och **resursgrupp**. För den **resurstyp**väljer **Azure Cosmos DB-konton**, väljer du något av dina befintliga Azure-Cosmos-konton och välj **tillämpa**. 

   ![Välj ett Cosmos DB-konto för att visa mått](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Därefter kan du välja ett mått i listan över tillgängliga mått. Du kan välja mått som är specifika för enheter för programbegäran, lagring, svarstider, tillgänglighet, Cassandra och andra. Mer information om alla tillgängliga mått i den här listan finns i den [mått efter kategori](#metrics-by-category) i den här artikeln. I det här exemplet väljer vi **programbegäran** och **genomsnittlig** som det sammanlagda värdet. 

   Förutom dessa uppgifter kan du också välja den **tidsintervall** och **tidskornighet** av mätvärden. På max, kan du visa mått för de senaste 30 dagarna.  När du använder filtret, visas ett diagram baserat på ditt filter. Du kan se det genomsnittliga antalet enheter för programbegäran som förbrukas per minut för den valda perioden.  

   ![Välj ett mått från Azure portal](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Lägga till filter för mått

Du kan också filtrera mått och diagram visas som en specifik **CollectionName**, **DatabaseName**, **OperationType**, **Region**, och **StatusCode**. Om du vill filtrera mått, Välj **Lägg till filter** och välj den obligatoriska egenskapen som **OperationType** och välj ett värde som **fråga**. Diagrammet visar enheter för programbegäran som förbrukas för frågeåtgärden för den valda perioden. De åtgärder som körs via lagrade proceduren loggas inte så att de inte är tillgängliga under OperationType mått.

![Lägg till ett filter för att välja mått precisionen](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Du kan gruppera mått med hjälp av den **gäller dela** alternativet. Du kan exempelvis gruppera enheter för programbegäran per åtgärdstyp och visa diagrammet för alla åtgärder samtidigt som du ser i följande bild: 

![Lägga till filtret delande](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Mått efter kategori

### <a name="request-metrics"></a>Begäran-mått
            
|Mått (mått visningsnamn)|Enhet (sammansättningstyp) |Beskrivning|Dimensioner| Tid Precision| Äldre mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| TotalRequests (totalt antal begäranden) | Count (antal) | Antalet begäranden som görs| DatabaseName, CollectionName, Region, StatusCode| Alla | TotalRequests Http 2xx, Http 3xx, Http 400, Http 401, interna serverfel, tjänsten är inte tillgänglig, begränsad begäranden, genomsnittlig begäranden per sekund | Används för att övervaka förfrågningar per statuskod, samling vid en minutersnivå. Genomsnittlig begäranden per sekund får använda antal aggregering på minut och dela med 60. |
| MetadataRequests (Metadataförfrågningar) |Count (antal) | Antalet metadataförfrågningar. Azure Cosmos DB upprätthåller samling för varje konto, som gör det möjligt att räkna upp samlingar, databaser osv., och konfigurationer, utan kostnad. | DatabaseName, CollectionName, Region, StatusCode| Alla| |Används för att övervaka begränsningar på grund av metadataförfrågningar.|
| MongoRequests (Mongo-begäranden) | Count (antal) | Antal Mongo-begäranden gjorda | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Alla |Mongo Query Request Rate, Mongo Update Request Rate, Mongo Delete Request Rate, Mongo Insert Request Rate, Mongo Count Request Rate| Används för att övervaka Mongo begäran fel, skriver du användningar per kommando. |

### <a name="request-unit-metrics"></a>Begär enhetsmått

|Mått (mått visningsnamn)|Enhet (sammansättningstyp)|Beskrivning|Dimensioner| Tid Precision| Äldre mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Frågeavgift) | Antal (totalt) |Mongo-programbegäran som förbrukas| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Alla |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Används för att övervaka Mongo resource ru: er på en minut.|
| TotalRequestUnits (totalt antal Begäransenheter)| Antal (totalt) | Enheter som används för begäran| DatabaseName, CollectionName, Region, StatusCode |Alla| TotalRequestUnits| Används för att övervaka Totalt antal RU användning vid en minutersnivå. Genomsnittlig RU som förbrukas per sekund får använda totala aggregering på minut och dela med 60.|
| ProvisionedThroughput (dataflöde)| Antal (maximalt) |Etablerat dataflöde med samling Precision| DatabaseName, samlingsnamn| 5M| | Används för att övervaka etablerat dataflöde per samling.|

### <a name="storage-metrics"></a>Storage-mått

|Mått (mått visningsnamn)|Enhet (sammansättningstyp)|Beskrivning|Dimensioner| Tid Precision| Äldre mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (Available Storage) |Byte (totalt) | Totalt tillgängligt lagringsutrymme per 5 minuter kornighet per region| DatabaseName, samlingsnamn, Region| 5M| Tillgängligt lagringsutrymme| Används för att övervaka tillgängligt lagringsutrymme kapacitet (gäller endast för fast storage samlingar) lägsta Granulariteten bör vara 5 minuter.| 
| DataUsage (dataanvändning) |Byte (totalt) |Total dataanvändning rapporteras med 5 minuter Precision per region| DatabaseName, samlingsnamn, Region| 5M |Datastorlek | Används för att övervaka total dataanvändning vid insamling och region, bör lägsta Granulariteten vara 5 minuter.|
| IndexUsage (Index användning) | Byte (totalt) |Total användning för Index rapporteras med 5 minuter Precision per region| DatabaseName, samlingsnamn, Region| 5M| Indexstorlek| Används för att övervaka total dataanvändning vid insamling och region, bör lägsta Granulariteten vara 5 minuter. |
| DocumentQuota (dokumentet Quota) | Byte (totalt) | Totala lagringskvoten rapporterade med 5 minuter Precision per region.| DatabaseName, samlingsnamn, Region| 5M |Lagringskapacitet| Används för att övervaka totala kvoten för insamling och region, bör lägsta Granulariteten vara 5 minuter.|
| DocumentCount (antal dokument) | Antal (totalt) |Antal totala dokument rapporteras med 5 minuter Precision per region| DatabaseName, samlingsnamn, Region| 5M |Antal dokument|Används för att övervaka dokumentantal i samlingen och region, bör lägsta Granulariteten vara 5 minuter.|

### <a name="latency-metrics"></a>Mått för datainmatningssvarstider

|Mått (mått visningsnamn)|Enhet (sammansättningstyp)|Beskrivning|Dimensioner| Tid Precision| Användning |
|---|---|---|---| ---| ---|
| ReplicationLatency (replikeringsfördröjning)| Millisekunder (lägsta, högsta, genomsnitt) | P99 replikeringsfördröjning mellan käll- och regioner för geo-aktiverat konto| SourceRegion, TargetRegion| Alla | Används för att övervaka P99 replikeringsfördröjning mellan de två regionerna för ett konto med geo-replikerade. |


### <a name="availability-metrics"></a>Mått på tillgänglighet

|Mått (mått visningsnamn) |Enhet (sammansättningstyp)|Beskrivning| Tid Precision| Äldre mått mappning | Användning |
|---|---|---|---| ---| ---|
| ServiceAvailability (tjänsttillgänglighet)| Procent (lägsta, högsta) | Tillgänglighet för begäranden med en timme Precision| 1H | Tjänsttillgänglighet | Representerar procent av totalen skickas begäranden. En begäran anses vara misslyckades på grund av systemfel om statuskoden är 410, 500 eller 503 som används för att övervaka tillgängligheten för konto på timme kornighet. |


### <a name="cassandra-api-metrics"></a>Cassandra-API-mått

|Mått (mått visningsnamn)|Enhet (sammansättningstyp)|Beskrivning|Dimensioner| Tid Precision| Användning |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra begäranden) | Count (antal) | Antal Cassandra API-begäranden gjorda| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Alla| Används för att övervaka Cassandra begäranden med en minut precision. Genomsnittlig begäranden per sekund får använda antal aggregering på minut och dela med 60.|
| CassandraRequestCharges (Cassandra-begäran-avgifter) | Antal (Sum, Min, Max, genomsn.) | Programbegäran som förbrukas av Cassandra API-begäranden| DatabaseName, CollectionName, Region, OperationType, ResourceType| Alla| Används för att övervaka ru: er som används per minut av ett Cassandra-API-konto.|
| CassandraConnectionClosures (Cassandra anslutning öppettider) |Count (antal) |Antal stängda Cassandra-anslutningar| ClosureReason, Region| Alla | Används för att övervaka anslutningen mellan klienter och Azure Cosmos DB Cassandra-API.|

## <a name="next-steps"></a>Nästa steg

* [Visa och övervaka mått från mått-fönstret för Azure Cosmos DB-konto](use-metrics.md)

* [Diagnostisk loggning i Azure Cosmos DB](logging.md)
