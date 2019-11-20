---
title: Övervaknings Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du övervakar prestanda och tillgänglighet för Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.openlocfilehash: acba365ac5cd33b6fbba5910e5e8b469bf111aac
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186856"
---
# <a name="monitoring-azure-cosmos-db"></a>Övervaknings Azure Cosmos DB
När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. Den här artikeln beskriver övervaknings data som genererats av Azure Cosmos-databaser och hur du kan använda funktionerna i Azure Monitor för att analysera och varna för dessa data.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
Azure Cosmos DB skapar övervaknings data med hjälp av [Azure Monitor](../azure-monitor/overview.md) som är en fullständig stack övervaknings tjänst i Azure som innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser, förutom resurser i andra moln och lokalt. 

Om du inte redan är bekant med att övervaka Azure-tjänster börjar du med artikeln [övervaka Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) som beskriver följande:

- Vad är Azure Monitor?
- Kostnader för övervakning
- Övervaknings data som samlas in i Azure
- Konfigurerar data insamling
- Standard verktyg i Azure för analys och avisering om övervaknings data

Följande avsnitt bygger på den här artikeln genom att beskriva de specifika data som samlats in från Azure Cosmos DB och tillhandahålla exempel för att konfigurera data insamling och analysera data med Azure-verktyg.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor för Cosmos DB (förhands granskning)
[Azure Monitor för Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) baseras på [arbets bokens funktion i Azure Monitor](../azure-monitor/app/usage-workbooks.md) och använder samma övervaknings data som samlas in för Cosmos DB som beskrivs i avsnitten nedan. Använd det här verktyget för att se övergripande prestanda, fel, kapacitet och drifts hälsa för alla dina Azure Cosmos DB resurser i en enhetlig interaktiv upplevelse och utnyttja de andra funktionerna i Azure Monitor för detaljerad analys och avisering. 

![Azure Monitor för Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Övervaknings data som samlas in från Azure Cosmos DB
Azure Cosmos DB samlar in samma typer av övervaknings data som andra Azure-resurser som beskrivs i [övervaknings data från Azure-resurser](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Se [Azure Cosmos DB övervaknings data referens](monitor-cosmos-db-reference.md) för en detaljerad referens för de loggar och mått som skapats av Azure Cosmos dB.

På sidan **Översikt** i Azure Portal för varje Azure Cosmos-databas finns en kort vy över databas användningen, inklusive begäran och fakturerings användningen per timme. Detta är användbar information, men bara en liten mängd tillgängliga övervaknings data. En del av dessa data samlas in automatiskt och är tillgängliga för analys så fort du skapar databasen, medan du kan aktivera ytterligare data insamling med en viss konfiguration.

![Översikts sida](media/monitor-cosmos-db/overview-page.png)



## <a name="diagnostic-settings"></a>Diagnostikinställningar
Plattforms mått och aktivitets loggen samlas in automatiskt, men du måste skapa en diagnostisk inställning för att samla in resurs loggar eller vidarebefordra dem utanför Azure Monitor. Mer information om hur du skapar en diagnostisk inställning med hjälp av Azure Portal, CLI eller PowerShell finns i [skapa diagnostisk inställning för att samla in plattforms loggar och statistik i Azure](../azure-monitor/platform/diagnostic-settings.md) .

När du skapar en diagnostisk inställning anger du vilka kategorier av loggar som ska samlas in. Kategorierna för Azure Cosmos DB visas nedan tillsammans med exempel data.

 * **DataPlaneRequests**: Välj det här alternativet om du vill logga backend-begäranden till alla API: er som innehåller SQL-, Graph-, MongoDB-, Cassandra-och tabell-API konton i Azure Cosmos dB. Nyckel egenskaper att observera är: Requestcharge, statusCode, clientIPaddress och partitionID.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Välj det här alternativet om du vill logga användarinitierade begär Anden från klient delen för att betjäna begär anden till Azure Cosmos DB s API för MongoDB. MongoDB-begäranden visas i MongoRequests och DataPlaneRequests. Nyckel egenskaper att observera är: Requestcharge, opCode.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: Välj det här alternativet om du vill logga frågetexten som kördes. 

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Välj det här alternativet om du vill logga statistik för partitionens nycklar. Detta visas för närvarande med lagrings storleken (KB) för partition nycklarna. Loggen genereras mot de första tre partitionsuppsättningar som upptar merparten data lagring.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **Mått begär Anden**: Välj det här alternativet om du vill samla in mått data från Azure Cosmos dB till målen i den diagnostiska inställningen. Detta är samma data som samlas in automatiskt i Azure-mått. Samla in Mät data med resurs loggar för att analysera båda typerna av data och skicka mått data utanför Azure Monitor.

## <a name="analyzing-metric-data"></a>Analysera mått data
Azure Cosmos DB ger en anpassad upplevelse för att arbeta med mått. Se [övervaka och felsök Azure Cosmos DB mått från Azure Monitor](cosmos-db-azure-monitor-metrics.md) för information om hur du använder den här upplevelsen och för att analysera olika Azure Cosmos DB scenarier.

Du kan analysera mått för Azure Cosmos DB med mått från andra Azure-tjänster med hjälp av Metric Explorer genom att öppna **mått** från **Azure Monitor** -menyn. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) . Alla mått för Azure Cosmos DB finns i namn området **Cosmos DB standard mått**. Du kan använda följande dimensioner med dessa mått när du lägger till ett filter i ett diagram:

- Samlingsnamn
- DatabaseName
- OperationType
- Region
- StatusCode


## <a name="analyzing-log-data"></a>Analysera loggdata
Data i Azure Monitor loggar lagras i tabeller som varje tabell har en egen uppsättning unika egenskaper. Azure Cosmos DB lagrar data i följande tabeller.

| Tabell | Beskrivning |
|:---|:---|
| AzureDiagnostics | Vanlig tabell som används av flera tjänster för att lagra resurs loggar. Resurs loggar från Azure Cosmos DB kan identifieras med `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Vanlig tabell som lagrar alla poster från aktivitets loggen. 


> [!IMPORTANT]
> När du väljer **loggar** från Azure Cosmos DB-menyn öppnas Log Analytics med fråge omfånget som är inställt på den aktuella Azure Cosmos-databasen. Det innebär att logg frågor bara innehåller data från den resursen. Om du vill köra en fråga som innehåller data från andra databaser eller data från andra Azure-tjänster väljer du **loggar** på **Azure Monitor** -menyn. Mer information finns i [logg frågans omfång och tidsintervall i Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="sample-queries"></a>Exempelfrågor
Följande är frågor som du kan använda för att övervaka dina Azure Cosmos-databaser.

* Fråga efter alla diagnostiska loggar från Azure Cosmos DB för en angiven tidsperiod:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Om du vill fråga efter 10 mest nyligen loggade händelser:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Fråga efter alla åtgärder, grupperade efter åtgärdstyp:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Att fråga efter alla åtgärder, grupperade efter **Resource**:

    ```Kusto
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Fråga efter alla användaraktivitet, grupperade efter resurs:

    ```Kusto
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
* För att få alla frågor som är större än 100 ru: er kopplade till data från **DataPlaneRequests** och **QueryRunTimeStatistics**.

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

* Fråga som åtgärder tar längre tid än 3 millisekunder:

    ```Kusto
    AzureDiagnostics | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Fråga för vilken agent kör åtgärder:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Fråga efter när de långvariga åtgärderna utfördes:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , duration_s | render timechart
    ```
    
* Så här hämtar du nyckel statistik för att utvärdera snedheten för de tre översta partitionerna för databas konto:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Övervaka Azure Cosmos DB programmässigt
De konto statistik tillgänglig i portalen, till exempel konto storage-användning och Totalt antal begäranden, är inte tillgängliga via SQL-API: er. Du kan dock hämta användningsdata på samlingsnivå med hjälp av SQL-API: er. Om du vill hämta data för samlingen på, gör du följande:

* Du använder REST-API [utför en hämtning på samlingen](https://msdn.microsoft.com/library/mt489073.aspx). Information om kvot och användning för samlingen returneras i x-ms-resource-quota- och x-ms--Resursanvändning i svaret.
* Om du vill använda .NET SDK använder den [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metod som returnerar en [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) som innehåller ett antal egenskaper som  **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**, med mera.

För att komma åt ytterligare mått, använda den [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Tillgängliga definitioner av mått kan hämtas genom att anropa:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Frågor att hämta enskilda mått använder du följande format:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Nästa steg

- Se [Azure Cosmos DB övervaknings data referens](monitor-cosmos-db-reference.md) för referenser till loggar och mått som skapats av Azure Cosmos dB.
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) .
