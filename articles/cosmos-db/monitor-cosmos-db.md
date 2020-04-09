---
title: Övervakning av Azure Cosmos DB | Microsoft-dokument
description: Lär dig hur du övervakar prestanda och tillgänglighet för Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: db9e86706ecd4e5b2526e1d801dda45ed6b345c6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887256"
---
# <a name="monitoring-azure-cosmos-db"></a>Övervakning av Azure Cosmos DB
När du har kritiska program och affärsprocesser som förlitar sig på Azure-resurser, vill du övervaka dessa resurser för deras tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervakningsdata som genereras av Azure Cosmos-databaser och hur du kan använda funktionerna i Azure Monitor för att analysera och avisera om dessa data.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
Azure Cosmos DB skapar övervakningsdata med Hjälp av [Azure Monitor](../azure-monitor/overview.md) som är en fullständig stackövervakningstjänst i Azure som tillhandahåller en komplett uppsättning funktioner för att övervaka dina Azure-resurser utöver resurser i andra moln och lokala. 

Om du inte redan är bekant med övervakning av Azure-tjänster börjar du med artikeln [Övervakning av Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) som beskriver följande:

- Vad är Azure Monitor?
- Kostnader i samband med övervakning
- Övervaka data som samlats in i Azure
- Konfigurera datainsamling
- Standardverktyg i Azure för analys och avisering av övervakningsdata

Följande avsnitt bygger på den här artikeln genom att beskriva specifika data som samlats in från Azure Cosmos DB och ge exempel för att konfigurera datainsamling och analysera dessa data med Azure-verktyg.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor för Cosmos DB (förhandsversion)
[Azure Monitor för Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) baseras på [arbetsboksfunktionen](../azure-monitor/app/usage-workbooks.md) i Azure Monitor och använder samma övervakningsdata som samlats in för Cosmos DB som beskrivs i avsnitten nedan. Använd det här verktyget för en vy över den övergripande prestanda, fel, kapacitet och drifthälsa för alla dina Azure Cosmos DB-resurser i en enhetlig interaktiv upplevelse och utnyttja de andra funktionerna i Azure Monitor för detaljerad analys och avisering. 

![Azure Monitor för Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Visa mätvärden på åtgärdsnivå för Azure Cosmos DB

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **Övervaka** i det vänstra **navigeringsfältet**och välj Mått .

   ![Fönstret Mått i Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. Välj **en resurs** > > välja den **prenumeration**och **resursgrupp**som krävs i fönstret **Mått.** För **resurstypen**väljer du **Azure Cosmos DB-konton,** väljer ett av dina befintliga Azure Cosmos-konton och väljer **Använd**.

   ![Välj ett Cosmos DB-konto för att visa mått](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Därefter kan du välja ett mått i listan över tillgängliga mått. Du kan välja mått som är specifika för att begära enheter, lagring, svarstid, tillgänglighet, Cassandra och andra. Mer information om alla tillgängliga mått i den här listan finns i artikeln [Mått efter kategori.](monitor-cosmos-db-reference.md) I det här exemplet ska vi välja **Begär enheter** och **Genomsnittlig** som aggregeringsvärde.

   Utöver dessa uppgifter kan du också välja **tidsintervall och** **tidsgranularitet** för måtten. På max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på filtret. Du kan se det genomsnittliga antalet begärandeenheter som förbrukas per minut för den valda perioden.  

   ![Välj ett mått från Azure-portalen](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Lägga till filter i mått

Du kan också filtrera mått och diagrammet som visas av ett visst **CollectionName,** **DatabaseName**, **OperationType**, **Region**och **StatusCode**. Om du vill filtrera måtten väljer du **Lägg till filter** och väljer den egenskap som krävs, till exempel **OperationType,** och väljer ett värde som **Fråga**. Diagrammet visar sedan de begärandeenheter som förbrukats för frågeåtgärden för den valda perioden. De åtgärder som utförs via lagrad procedur loggas inte så att de inte är tillgängliga under OperationType-måttet.

![Lägga till ett filter för att välja måttgranularitet](./media/monitor-cosmos-db/add-metrics-filter.png)

Du kan gruppera mått med alternativet **Använd delning.** Du kan till exempel gruppera begäranheterna per operationstyp och visa diagrammet för alla operationer samtidigt som visas i följande bild:

![Lägg till delningsfilter för apply](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Övervaka data som samlats in från Azure Cosmos DB

Azure Cosmos DB samlar in samma typer av övervakningsdata som andra Azure-resurser som beskrivs i [Övervakningsdata från Azure-resurser](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Se [Azure Cosmos DB övervakningsdatareferens](monitor-cosmos-db-reference.md) för en detaljerad referens av loggar och mått som skapats av Azure Cosmos DB.

**Översiktssidan** i Azure-portalen för varje Azure Cosmos-databas innehåller en kort vy över databasanvändningen, inklusive dess begäran och faktureringsanvändning per timme. Detta är användbar information, men bara en liten mängd av tillgängliga övervakningsdata. En del av dessa data samlas in automatiskt och är tillgängliga för analys så fort du skapar databasen medan du kan aktivera ytterligare datainsamling med viss konfiguration.

![Översiktssida](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analysera måttdata

Azure Cosmos DB ger en anpassad upplevelse för att arbeta med mått. Se [Övervaka och felsöka Azure Cosmos DB-mått från Azure Monitor](cosmos-db-azure-monitor-metrics.md) för information om hur du använder den här upplevelsen och för att analysera olika Azure Cosmos DB-scenarier.

Du kan analysera mått för Azure Cosmos DB med mått från andra Azure-tjänster med hjälp av Metrics Explorer genom att öppna **mått** från **Azure Monitor-menyn.** Mer information om hur du använder det här verktyget finns [i Komma igång med Azure Metrics Explorer.](../azure-monitor/platform/metrics-getting-started.md) Alla mått för Azure Cosmos DB finns i **standardmåtten**namespace Cosmos DB . Du kan använda följande dimensioner med dessa mått när du lägger till ett filter i ett diagram:

- CollectionName
- DatabaseName
- OperationType (olikartade)
- Region
- Statuskod


## <a name="analyzing-log-data"></a>Analysera loggdata
Data i Azure Monitor Logs lagras i tabeller som varje tabell har sin egen uppsättning unika egenskaper. Azure Cosmos DB lagrar data i följande tabeller.

| Tabell | Beskrivning |
|:---|:---|
| AzureDiagnostics | Vanliga tabell som används av flera tjänster för att lagra resursloggar. Resursloggar från Azure Cosmos DB `MICROSOFT.DOCUMENTDB`kan identifieras med .   |
| AzureActivity    | Gemensam tabell som lagrar alla poster från aktivitetsloggen. 


> [!IMPORTANT]
> När du väljer **Loggar** på Azure Cosmos DB-menyn öppnas Log Analytics med frågeomfattningen inställd på den aktuella Azure Cosmos-databasen. Det innebär att loggfrågor endast innehåller data från den resursen. Om du vill köra en fråga som innehåller data från andra databaser eller data från andra Azure-tjänster väljer du **Loggar** på **Azure Monitor-menyn.** Mer information finns [i Loggfrågeomfattning och tidsintervall i Azure Monitor Log Analytics.](../azure-monitor/log-query/scope.md)

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics-frågor i Azure Monitor

Här är några frågor som du kan ange i **sökfältet logga** in för att hjälpa dig att övervaka dina Azure Cosmos-behållare. Dessa frågor fungerar med det [nya språket](../log-analytics/log-analytics-log-search-upgrade.md).

Följande är frågor som du kan använda för att hjälpa dig att övervaka dina Azure Cosmos-databaser.

* Så här frågar du efter alla diagnostikloggar från Azure Cosmos DB under en angiven tidsperiod:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Så här frågar du efter de 10 senast loggade händelserna:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Så här frågar du efter alla åtgärder, grupperade efter åtgärdstyp:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Så här frågar du efter alla åtgärder, grupperade efter resurs:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Så här frågar du efter all användaraktivitet, grupperad efter resurs:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* För att få alla frågor större än 100 RU anslutna med data från **DataPlaneRequests** och **QueryRunTimeStatistics**.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* Så här frågar du för vilka åtgärder som tar längre tid än 3 millisekunder:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Så här frågar du efter vilken agent som kör åtgärderna:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Så här frågar du efter när de tidskrävande åtgärderna utfördes:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Så här hämtar du statistik för partitionsnyckel för att utvärdera skeva över de tre översta partitionerna för databaskonto:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Övervaka Azure Cosmos DB programmässigt
De kontonivåmått som är tillgängliga i portalen, till exempel kontolagringsanvändning och totala begäranden, är inte tillgängliga via SQL-API:erna. Du kan dock hämta användningsdata på insamlingsnivå med hjälp av SQL-API:erna. Så här hämtar du data på insamlingsnivå:

* Om du vill använda REST API [utför du en GET på samlingen](https://msdn.microsoft.com/library/mt489073.aspx). Kvot- och användningsinformationen för samlingen returneras i x-ms-resurs-kvot- och x-ms-resursanvändningshuvudena i svaret.
* Om du vill använda .NET SDK använder du metoden [DocumentClient.ReadDocumentCollectionAsync,](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) som returnerar ett [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) som innehåller ett antal användningsegenskaper som **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**med mera.

Använd [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)för att komma åt ytterligare mått . Tillgängliga måttdefinitioner kan hämtas genom att anropa:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Frågor för att hämta enskilda mått använder följande format:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Nästa steg

- Se [Azure Cosmos DB övervakningsdatareferens](monitor-cosmos-db-reference.md) för en referens till loggar och mått som skapats av Azure Cosmos DB.
- Mer information om hur du övervakar Azure-resurser finns i [Övervaka Azure-resurser med Azure-resurser.](../azure-monitor/insights/monitor-azure-resource.md)
