---
title: Övervaknings Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du övervakar prestanda och tillgänglighet för Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: c04d61c6381304382261219f23e99401465590ca
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099393"
---
# <a name="monitoring-azure-cosmos-db"></a>Övervaknings Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. Den här artikeln beskriver övervaknings data som genererats av Azure Cosmos-databaser och hur du kan använda funktionerna i Azure Monitor för att analysera och varna för dessa data.

Du kan övervaka dina data med mått på klient sidan och Server sidan. När du använder mått på Server sidan kan du övervaka de data som lagras i Azure Cosmos DB med följande alternativ:

* **Övervaka från Azure Cosmos DB Portal:** Du kan övervaka med de mått som är tillgängliga på fliken **mått** i Azure Cosmos-kontot. Måtten på den här fliken är data flöde, lagring, tillgänglighet, latens, konsekvens och system nivå mått. Som standard har dessa mått en kvarhållningsperiod på 7 dagar. Läs mer i avsnittet [övervaknings data som samlas in från Azure Cosmos DB](#monitoring-from-azure-cosmos-db) i den här artikeln.

* **Övervaka med mått i Azure Monitor:** Du kan övervaka måtten för ditt Azure Cosmos-konto och skapa instrument paneler från Azure Monitor. Azure Monitor samlar in Azure Cosmos DB mått som standard har du inte konfigurerat något explicit. Dessa mått samlas in med en-minuters kornig het, medan kornigheten kan variera beroende på vilket mått du väljer. Som standard har dessa mått en kvarhållningsperiod på 30 dagar. De flesta mått som är tillgängliga från föregående alternativ är också tillgängliga i dessa mått. Dimensionsvärdena för måtten, till exempel container Name, är Skift läges okänsliga. Du måste därför använda Skift läges okänslig jämförelse när du utför sträng jämförelser på dessa dimensions värden. Mer information finns i avsnittet [analysera mått data](#analyze-metric-data) i den här artikeln.

* **Övervaka med diagnostikloggar i Azure Monitor:** Du kan övervaka loggarna för ditt Azure Cosmos-konto och skapa instrument paneler från Azure Monitor. Telemetri som händelser och spår som inträffar vid en andra kornig het lagras som loggar. Till exempel, om data flödet för en behållare är ändringar, ändras egenskaperna för ett Cosmos-konto och dessa händelser samlas in i loggarna. Du kan analysera dessa loggar genom att köra frågor på insamlade data. Mer information finns i avsnittet [analysera logg data](#analyze-log-data) i den här artikeln.

* **Övervaka program mässigt med SDK: er:** Du kan övervaka ditt Azure Cosmos-konto via programmering med hjälp av .NET, Java, python, Node.js SDK: er och rubrikerna i REST API. Mer information finns i avsnittet [övervakning Azure Cosmos DB program mässigt](#monitor-cosmosdb-programmatically) i den här artikeln.

Följande bild visar olika alternativ som är tillgängliga för att övervaka Azure Cosmos DB konto via Azure Portal:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Tillgängliga övervaknings alternativ i Azure Portal" border="false":::

När du använder Azure Cosmos DB kan du på klient sidan samla in information om begär ande avgift, aktivitets-ID, undantag/stack spårnings information, HTTP-status/under status kod, diagnostisk sträng för att felsöka eventuella problem som kan uppstå. Den här informationen krävs också om du behöver kontakta Azure Cosmos DB support-teamet.  

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?

Azure Cosmos DB skapar övervaknings data med hjälp av [Azure Monitor](../azure-monitor/overview.md) som är en fullständig stack övervaknings tjänst i Azure som innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser, förutom resurser i andra moln och lokalt.

Om du inte redan är bekant med att övervaka Azure-tjänster börjar du med artikeln [övervaka Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) som beskriver följande:

* Vad är Azure Monitor?
* Kostnader för övervakning
* Övervaknings data som samlas in i Azure
* Konfigurerar data insamling
* Standard verktyg i Azure för analys och avisering om övervaknings data

Följande avsnitt bygger på den här artikeln genom att beskriva de specifika data som samlats in från Azure Cosmos DB och tillhandahålla exempel för att konfigurera data insamling och analysera data med Azure-verktyg.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Monitor för Azure Cosmos DB

Azure Monitor för Azure Cosmos DB baseras på [arbets bokens funktion i Azure Monitor](../azure-monitor/platform/workbooks-overview.md) och använder samma övervaknings data som samlas in för Cosmos DB som beskrivs i avsnitten nedan. Använd Azure Monitor för att se övergripande prestanda, fel, kapacitet och drifts hälsa för alla dina Azure Cosmos DB resurser i en enhetlig interaktiv upplevelse och utnyttja de andra funktionerna i Azure Monitor för detaljerad analys och avisering. Mer information finns i artikeln [utforska Azure Monitor för Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) .

> [!NOTE]
> När du skapar behållare ser du till att du inte skapar två behållare med samma namn men med olika Skift läge. Det beror på att vissa delar av Azure-plattformen inte är Skift läges känsliga, och det kan leda till förvirring/kollisioner för telemetri och åtgärder på behållare med sådana namn.

## <a name="monitor-data-collected-from-azure-cosmos-db-portal"></a><a id="monitoring-from-azure-cosmos-db"></a> Övervaka data som samlas in från Azure Cosmos DB Portal

Azure Cosmos DB samlar in samma typer av övervaknings data som andra Azure-resurser som beskrivs i [övervaknings data från Azure-resurser](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Se [Azure Cosmos DB övervaknings data referens](monitor-cosmos-db-reference.md) för en detaljerad referens för de loggar och mått som skapats av Azure Cosmos dB.

På sidan **Översikt** i Azure Portal för varje Azure Cosmos-databas finns en kort vy över databas användningen, inklusive begäran och fakturerings användningen per timme. Detta är användbar information, men bara en liten mängd tillgängliga övervaknings data. En del av dessa data samlas in automatiskt och är tillgängliga för analys så fort du skapar databasen, medan du kan aktivera ytterligare data insamling med en viss konfiguration.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Tillgängliga övervaknings alternativ i Azure Portal":::

## <a name="analyzing-metric-data"></a><a id="analyze-metric-data"></a> Analysera mått data

Azure Cosmos DB ger en anpassad upplevelse för att arbeta med mått.

Du kan analysera mått för Azure Cosmos DB med mått från andra Azure-tjänster med hjälp av Metric Explorer genom att öppna **mått** från **Azure Monitor** -menyn. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) . Alla mått för Azure Cosmos DB finns i namn området **Cosmos DB standard mått** . Du kan använda följande dimensioner med dessa mått när du lägger till ett filter i ett diagram:

* CollectionName
* DatabaseName
* Åtgärdstyp
* Region
* StatusCode

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Visa mått på åtgärds nivå för Azure Cosmos DB

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **övervaka** i det vänstra navigerings fältet och välj **mått** .

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Tillgängliga övervaknings alternativ i Azure Portal":::

1. I fönstret **mått** > väljer du **en resurs** > väljer den nödvändiga **prenumerationen** och **resurs gruppen** . För **resurs typen** väljer du **Azure Cosmos DB konton** , väljer något av dina befintliga Azure Cosmos-konton och väljer **Använd** .

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Tillgängliga övervaknings alternativ i Azure Portal":::

1. Sedan kan du välja ett mått i listan över tillgängliga mått. Du kan välja mått som är speciella för att begära enheter, lagring, svars tid, tillgänglighet, Cassandra och andra. Mer information om alla tillgängliga mått i den här listan finns i artikeln [mått per kategori](monitor-cosmos-db-reference.md) . I det här exemplet ska vi välja **enheter för programbegäran** och **AVG** som agg regerings värde.

   Förutom dessa uppgifter kan du också välja **tidsintervallet** och **tids kornig het** för måtten. Som Max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på ditt filter. Du kan se det genomsnittliga antalet förbrukade enheter för programbegäran per minut för den valda perioden.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Tillgängliga övervaknings alternativ i Azure Portal":::

### <a name="add-filters-to-metrics"></a>Lägg till filter till mått

Du kan också filtrera mått och diagrammet som visas av en speciell **samlings** -, **databasename** -, **OperationType** -, **region** -och **StatusCode** -värde. Om du vill filtrera måtten väljer du **Lägg till filter** och väljer önskad egenskap som **OperationType** och väljer ett värde, till exempel **fråga** . Diagrammet visar sedan de enheter för programbegäran som för bruk ATS för den valda perioden. De åtgärder som utförs via den lagrade proceduren loggas inte, så de är inte tillgängliga i OperationType-måttet.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Tillgängliga övervaknings alternativ i Azure Portal":::

Du kan gruppera mått med hjälp av alternativet **Använd delning** . Du kan till exempel gruppera enheter för programbegäran per åtgärds typ och Visa grafen för alla åtgärder samtidigt som visas i följande bild:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Tillgängliga övervaknings alternativ i Azure Portal":::

## <a name="analyzing-log-data"></a><a id="analyze-log-data"></a> Analysera loggdata

Data i Azure Monitor loggar lagras i tabeller som varje tabell har en egen uppsättning unika egenskaper. Azure Cosmos DB lagrar data i följande tabeller.

| Tabell | Beskrivning |
|:---|:---|
| AzureDiagnostics | Vanlig tabell som används av flera tjänster för att lagra resurs loggar. Resurs loggar från Azure Cosmos DB kan identifieras med `MICROSOFT.DOCUMENTDB` .   |
| AzureActivity    | Vanlig tabell som lagrar alla poster från aktivitets loggen.

> [!IMPORTANT]
> När du väljer **loggar** från Azure Cosmos DB-menyn öppnas Log Analytics med fråge omfånget som är inställt på den aktuella Azure Cosmos-databasen. Det innebär att logg frågor bara innehåller data från den resursen. Om du vill köra en fråga som innehåller data från andra databaser eller data från andra Azure-tjänster väljer du **loggar** på **Azure Monitor** -menyn. Mer information finns i [logg frågans omfång och tidsintervall i Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics frågor i Azure Monitor

Här följer några frågor som du kan ange i Sök fältet för **loggs ökning** och som hjälper dig att övervaka dina Azure Cosmos-behållare. Dessa frågor fungerar med det [nya språket](../azure-monitor/log-query/log-query-overview.md).

Följande är frågor som du kan använda för att övervaka dina Azure Cosmos-databaser.

* Fråga efter alla diagnostikloggar från Azure Cosmos DB under en angiven tids period:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* För att fråga efter alla åtgärder, grupperat efter resurs:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* För att fråga efter all användar aktivitet, grupperad efter resurs:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Övervaka Azure Cosmos DB program mässigt

Konto nivå måtten som är tillgängliga i portalen, till exempel användning av konto lagring och totalt antal begär Anden, är inte tillgängliga via SQL-API: erna. Du kan dock hämta användnings data på samlings nivå med hjälp av SQL-API: erna. Gör följande för att hämta data på samlings nivå:

* Om du vill använda REST API [utför du en hämtning på samlingen](/rest/api/cosmos-db/get-a-collection). Kvot-och användnings informationen för samlingen returneras i rubrikerna x-MS-Resource-quota och x-MS-Resource-Usage i svaret.

* Om du vill använda .NET SDK använder du metoden [DocumentClient. ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) , som returnerar en [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1) som innehåller ett antal användnings egenskaper som **CollectionSizeUsage** , **DatabaseUsage** , **DocumentUsage** med flera.

Använd [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)för att få åtkomst till ytterligare mått. Tillgängliga mått definitioner kan hämtas genom att anropa:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Använd följande format för att hämta enskilda mått:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

Mer information finns i artikeln [Azure monitoring REST API](../azure-monitor/platform/rest-api-walkthrough.md) .

## <a name="next-steps"></a>Nästa steg

* Se [Azure Cosmos DB övervaknings data referens](monitor-cosmos-db-reference.md) för referenser till loggar och mått som skapats av Azure Cosmos dB.
* Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) .