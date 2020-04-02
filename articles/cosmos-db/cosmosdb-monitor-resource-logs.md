---
title: Övervaka Azure Cosmos DB-data med hjälp av Azure Diagnostic-inställningar
description: Lär dig hur du använder Azure Diagnostic-inställningar för att övervaka prestanda och tillgänglighet för data som lagras i Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: f5a0b0f71a72ea76940450f73354fda230e09c5c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521050"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Övervaka Azure Cosmos DB-data med hjälp av diagnostikinställningar i Azure

Diagnostikinställningar i Azure används för att samla in resursloggar. Azure-resursloggar genereras av en resurs och tillhandahåller omfattande, frekventa data om resursens funktion. Dessa loggar fångas in per begäran och de kallas även "dataplanloggar". Några exempel på dataplanåtgärderna är borttagning, infoga och readFeed. Innehållet i dessa loggar varierar beroende på resurstyp.

Plattformsmått och aktivitetsloggar samlas in automatiskt, medan du måste skapa en diagnostikinställning för att samla in resursloggar eller vidarebefordra dem utanför Azure Monitor. Du kan aktivera diagnostikinställning för Azure Cosmos-konton med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Navigera till ditt Azure Cosmos-konto. Öppna fönstret **Diagnostikinställningar** och välj sedan Alternativet **Lägg till diagnostikinställning.**

1. Fyll i formuläret med följande information i fönstret **Diagnostikinställningar:** 

    * **Namn**: Ange ett namn på de loggar som ska skapas.

    * Du kan lagra loggarna för att **arkivera till ett lagringskonto,** **Strömma till en händelsehubb** eller **Skicka till Logganalys**

1. När du skapar en diagnostikinställning anger du vilken kategori av loggar som ska samlas in. Kategorierna av loggar som stöds av Azure Cosmos DB visas nedan tillsammans med exempellogg som samlas in av dem:

 * **DataPlaneRequests**: Välj det här alternativet för att logga backend-begäranden till alla API:er, som inkluderar SQL-, Graph-, MongoDB-, Cassandra- och Table API-konton i Azure Cosmos DB. Viktiga egenskaper att `Requestcharge`notera `statusCode` `clientIPaddress`är: `partitionID`, , och .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Välj det här alternativet för att logga användarinitierade begäranden från klientdelen för att betjäna begäranden till Azure Cosmos DB:s API för MongoDB. Den här loggtypen är inte tillgänglig för andra API-konton. Viktiga egenskaper att `Requestcharge`notera `opCode`är: , . När du aktiverar MongoRequests i diagnostikloggar, se till att stänga av DataPlaneRequests. Du skulle se en logg för varje begäran som görs på API: et.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests**: Välj det här alternativet för att logga användarinitierade begäranden från klientdelen för att betjäna begäranden till Azure Cosmos DB:s API för Cassandra. Den här loggtypen är inte tillgänglig för andra API-konton. De viktigaste egenskaperna `operationName`som `requestCharge` `piiCommandText`ska observeras är , , . När du aktiverar CassandraRequests i diagnostikloggar, se till att stänga av DataPlaneRequests. Du skulle se en logg för varje begäran som görs på API: et.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **QueryRuntimeStatistics**: Välj det här alternativet om du vill logga frågetexten som kördes. Den här loggtypen är endast tillgänglig för SQL API-konton.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Välj det här alternativet för att logga statistik för partitionsnycklarna. Detta representeras för närvarande med lagringsstorleken (KB) för partitionsnycklarna. Se [felsökningsproblemen med hjälp av](#diagnostic-queries) azure diagnostic queries i den här artikeln. Till exempel frågor som använder "PartitionKeyStatistics". Loggen avges mot de tre första partitionsnycklarna som upptar de flesta datalagring. Den här loggen innehåller data som prenumerations-ID, regionnamn, databasnamn, samlingsnamn, partitionsnyckel och lagringsstorlek i KB.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: Den här loggen rapporterar den aggregerade PER SEKUND RU/s förbrukning av partitionsnycklar. Azure Cosmos DB rapporterar partitionsnycklar endast för SQL API-konton och för punktläsning/skrivning och lagrad proceduråtgärder. andra API:er och åtgärdstyper stöds inte. För andra API:er är partitionsnyckelkolumnen i diagnostikloggtabellen tom. Den här loggen innehåller data som prenumerations-ID, regionnamn, databasnamn, samlingsnamn, partitionsnyckel, åtgärdstyp och begärandeavgift. Se [felsökningsproblemen med hjälp av](#diagnostic-queries) azure diagnostic queries i den här artikeln. Till exempel frågor som använder "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: Den här loggen innehåller information om kontrollplansåtgärder som att skapa ett konto, lägga till eller ta bort en region, uppdatera inställningar för kontoreplikering etc. Den här loggtypen är tillgänglig för alla API-typer som innehåller SQL (Core), MongoDB, Gremlin, Cassandra, Table API.

* **Begäranden:** Välj det här alternativet för att samla in måttdata från Azure Cosmos DB till destinationerna i diagnostikinställningen. Detta är samma data som samlas in automatiskt i Azure Metrics. Samla in måttdata med resursloggar för att analysera båda typerna av data tillsammans och för att skicka måttdata utanför Azure Monitor.

Detaljerad information om hur du skapar en diagnostikinställning med hjälp av Azure-portalen, CLI eller PowerShell finns i [Skapa diagnostikinställning för att samla in plattformsloggar och mått i](../azure-monitor/platform/diagnostic-settings.md) Azure-artikeln.


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>Felsöka problem med diagnostikfrågor

1. Hur får man begäran avgifter för dyra frågor?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Hur hittar man vilka verksamheter som tar det mesta av RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Hur får man distributionen för olika operationer?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Vad är det maximala dataflödet som en partition har förbrukat?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Hur får man information om partitionsnycklarNA RU /s förbrukning per sekund?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Så här hämtar du begäranden för en specifik partitionsnyckel

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Hur får man de översta partitionsnycklarna med de flesta RU /s förbrukas under en viss period? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Hur får man loggarna för partitionsnycklarna vars lagringsstorlek är större än 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Hur får partition Nyckel statistik för att utvärdera skeva över de tre översta partitioner för databaskonto?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor för Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Övervaka och felsöka med mått i Azure Cosmos DB](use-metrics.md)
