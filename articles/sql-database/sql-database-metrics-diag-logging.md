---
title: "Azure SQL database mått och diagnostikloggning | Microsoft Docs"
description: "Lär dig mer om hur du konfigurerar Azure SQL Database för att lagra Resursanvändning, anslutning och statistik för körning av frågan."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: vvasic
ms.openlocfilehash: 9f201454d58dbc646923d0155ff41761d593ab7e
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database-mätvärden och diagnostikloggning 
Azure SQL Database kan generera mätvärden och diagnostikfunktionerna loggar för lättare övervakning. Du kan konfigurera SQL-databasen för att lagra resursanvändning, personal och sessioner och anslutning till en av dessa Azure-resurser:

* **Azure Storage**: används för arkivering av stora mängder telemetri för små pris.
* **Händelsehubbar i Azure**: används för att integrera SQL Database telemetri med anpassade övervakningslösning eller varm pipelines.
* **Azure logganalys**: används för en out box övervakningslösning med reporting, varningar och minimera funktioner.

    ![Arkitektur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Aktivera loggning

Mätvärden och diagnostikfunktionerna loggning är inte aktiverad som standard. Du kan aktivera och hantera mått och diagnostik loggning genom att använda någon av följande metoder:

- Azure Portal
- PowerShell
- Azure CLI
- Azure-Monitor REST API 
- Azure Resource Manager-mall

Du måste ange Azure-resurs där valda data samlas in när du aktiverar mått och diagnostikloggning. Alternativen är:

- Log Analytics
- Händelsehubbar
- Lagring 

Du kan etablera en ny resurs i Azure eller välj en befintlig resurs. Du måste ange vilka data som ska samlas in när du har valt storage-resursen. Alternativen är:

- [Alla mätvärden](sql-database-metrics-diag-logging.md#all-metrics): innehåller DTU-procent, DTU gränsen, CPU-procent fysiska data läsa procent, log skriva procent, lyckade/misslyckade/Blocked av brandväggens anslutningar, sessioner procent, arbetare procent, lagring, lagringsprocent , och XTP lagringsprocent.
- [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): innehåller information om frågan runtime statistik, till exempel CPU-användning och fråga varaktighet.
- [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): innehåller information om frågan vänta statistik, som talar om dina frågor väntat, t.ex CPU, LOGG och LÅSET.
- [Fel](sql-database-metrics-diag-logging.md#errors-dataset): innehåller information om SQL-fel som inträffat på den här databasen.
- [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-waits-dataset): innehåller information om hur lång tid en databas har använt väntar på olika vänta typer.
- [Timeout](sql-database-metrics-diag-logging.md#timeouts-dataset): innehåller information om fel som inträffat på en databas.
- [Blockings](sql-database-metrics-diag-logging.md#blockings-dataset): innehåller information om att blockera händelser som inträffade på en databas.
- [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): innehåller Intelligent insikter. [Mer information om Intelligent insikter](sql-database-intelligent-insights.md).

Om du väljer Händelsehubbar eller ett lagringskonto måste ange du en bevarandeprincip. Den här principen tar bort data som är äldre än en vald tidsperiod. Om du anger logganalys beror bevarandeprincipen på den valda prisnivån. Mer information finns i [logganalys priser](https://azure.microsoft.com/pricing/details/log-analytics/). 

Information om hur du aktiverar loggning och förstå mått och logga kategorier som stöds av olika Azure-tjänster rekommenderar vi att du läser: 

* [Översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Översikt över Azure diagnostics loggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

### <a name="azure-portal"></a>Azure Portal

1. Om du vill aktivera mätvärden och diagnostikfunktionerna loggar samling i portalen, gå till din SQL-databas eller en elastisk pool sida och välj sedan **diagnostikinställningarna**.

   ![Aktivera i Azure-portalen](./media/sql-database-metrics-diag-logging/enable-portal.png)

2. Skapa nya eller redigera befintliga diagnostikinställningar genom att välja mål och telemetrin.

   ![Diagnostikinställningar](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

Om du vill aktivera mätvärden och diagnostikfunktionerna loggning med hjälp av PowerShell använder du följande kommandon:

- Använd följande kommando för att aktivera lagring av diagnostik loggar i ett lagringskonto:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Storage-konto-ID är resurs-ID för lagringskontot där du vill skicka loggar.

- Om du vill aktivera strömning av diagnostik loggar till en händelsehubb, Använd följande kommando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Regel-ID för Azure Service Bus är en sträng med formatet:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Använd följande kommando om du vill aktivera skickas diagnostik loggar till en logganalys-arbetsyta:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Du kan hämta resurs-ID för logganalys-arbetsytan med hjälp av följande kommando:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Du kan kombinera dessa parametrar för att aktivera flera alternativ för utdata.

### <a name="to-configure-multiple-azure-resources"></a>Så här konfigurerar du Azure-resurser

För att stödja flera prenumerationer, använder du PowerShell-skriptet från [aktivera Azure resource mått loggning med hjälp av PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Ange arbetsytan resurs-ID &lt;$WSID&gt; som en parameter när du kör skriptet (aktivera AzureRMDiagnostics.ps1) att skicka diagnostikdata från flera resurser till arbetsytan. Att hämta arbetsyte-ID &lt;$WSID&gt; vill som du vill skicka diagnostikdata, ersätter &lt;subID&gt; med prenumerations-ID ersätta &lt;RG_NAME&gt; med resursgruppens namn och Ersätt &lt;WS_NAME&gt; med namnet på arbetsytan i skriptet nedan.

- Använd följande kommandon för att konfigurera Azure-resurser:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Om du vill aktivera mätvärden och diagnostikfunktionerna loggning med hjälp av Azure CLI, använder du följande kommandon:

- Använd följande kommando för att aktivera lagring av diagnostik loggar i ett lagringskonto:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   Storage-konto-ID är resurs-ID för lagringskontot där du vill skicka loggar.

- Om du vill aktivera strömning av diagnostik loggar till en händelsehubb, Använd följande kommando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Regel-ID för Service Bus är en sträng med formatet:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Använd följande kommando om du vill aktivera skickas diagnostik loggar till en logganalys-arbetsyta:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Du kan kombinera dessa parametrar för att aktivera flera alternativ för utdata.

### <a name="rest-api"></a>REST API

Läs mer om hur du [ändra diagnostikinställningarna med hjälp av REST API för Azure-Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Resource Manager-mall

Läs mer om hur du [aktivera diagnostikinställningar när resursen skapas med hjälp av en Resource Manager-mall](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Dataströmmen till logganalys 
SQL-databas mätvärden och diagnostikfunktionerna loggar kan strömmas till Log Analytics med hjälp av inbyggt **skicka till logganalys** alternativet i portalen. Du kan också aktivera Log Analytics med hjälp av en inställning för diagnostik via PowerShell-cmdlets, Azure CLI eller Azure-Monitor REST-API.

### <a name="installation-overview"></a>Installationsöversikt

Övervakning av en SQL-databas flotta är enkelt med logganalys. Tre steg krävs:

1. Skapa en Log Analytics-resurs.

2. Konfigurera databaser för att registrera mätvärden och diagnostikfunktionerna loggar i logganalys-resurs som du skapat.

3. Installera den **Azure SQL Analytics** lösningar från galleriet i logganalys.

### <a name="create-a-log-analytics-resource"></a>Skapa en resurs för logganalys

1. Välj **ny** på menyn till vänster.

2. Välj **övervakning + Management**.

3. Välj **logga Analytics**.

4. Fyll i formuläret logganalys med ytterligare information som krävs: arbetsytans namn, prenumeration, resursgrupp, plats och prisnivån.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurera databaserna så att registrera mätvärden och diagnostikfunktionerna loggar

Det enklaste sättet att konfigurera där databaserna registrera sina mått är via Azure-portalen. Gå till din SQL Database-resurs i portalen och välj **diagnostikinställningarna**. 

### <a name="install-the-sql-analytics-solution-from-the-gallery"></a>Installera SQL Analytics-lösning från galleriet

1. När du skapar resursen logganalys och dina data flödar till den, installerar du SQL Analytics-lösning. Välj på sidan Operations Management Suite startsida på menyn sida **lösningar galleriet**. I galleriet, väljer du den **Azure SQL Analytics** lösning och välj **Lägg till**.

   ![övervakningslösning](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. På startsidan Operations Management Suite den **Azure SQL Analytics** panelen visas. Välj den här rutan för att öppna SQL Analytics-instrumentpanelen.

### <a name="use-the-sql-analytics-solution"></a>Använda SQL Analytics-lösning

SQL-Analytics är en hierarkisk instrumentpanel där du kan gå igenom hierarkin för SQL Database-resurser. Information om hur du använder SQL Analytics-lösningen finns [övervakaren SQL-databas med hjälp av SQL-Analytics lösningen](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Dataströmmen i Händelsehubbar

SQL-databas mätvärden och diagnostikfunktionerna loggar kan strömmas till Händelsehubbar med hjälp av inbyggt **dataströmmen till en händelsehubb** alternativet i portalen. Du kan också aktivera regel-ID för Service Bus med hjälp av en inställning för diagnostik via PowerShell-cmdlets, Azure CLI eller Azure-Monitor REST-API. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Vad du gör med mätvärden och diagnostikfunktionerna loggar i Händelsehubbar
När valda data strömmas i Händelsehubbar, är ett steg till att aktivera scenarion med avancerad övervakning. Händelsehubbar fungerar som ytterdörren för en händelsepipeline. När data har samlats in i en händelsehubb, kan de omvandlas och lagras med hjälp av en leverantör av realtidsanalys eller adaptrar för batchbearbetning/lagring. Händelsehubbar frikopplar produktionen av en händelseström från användningen av dessa händelser. På så sätt kan händelsekonsumenterna kan komma åt dem på sitt eget schema. Mer information om Händelsehubbar finns:

- [Vad är Händelsehubbar i Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Här följer några sätt att du kan använda strömmande funktionen:

* **Visa tjänstens hälsa med direktuppspelning hot sökväg till Power BI**. Du kan enkelt omvandla mätvärden och diagnostikfunktionerna data till nära realtidsinsikter på Azure-tjänster med hjälp av Händelsehubbar, Stream Analytics och Power BI. En översikt över hur du ställer in en händelsehubb, bearbetning av data med Stream Analytics och Använd Power BI som utdata, finns i [Stream Analytics och Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Strömma loggar till tredje parts loggning och telemetri dataströmmar**. Du kan få mätvärden och diagnostikfunktionerna loggarna till olika övervaknings- och log analytics tredjepartslösningar med Händelsehubbar strömning. 

* **Skapa en anpassad telemetri och loggning plattform**. Om du redan har en specialbyggt telemetri plattform eller funderar på att skapa en mycket skalbar publicera och prenumerera uppbyggnad Händelsehubbar kan du flexibelt kan mata in diagnostik loggar. Se [Dan Rosanova guide med Händelsehubbar i en global skala telemetri platform](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Dataströmmen lagring

SQL-databas mätvärden och diagnostikfunktionerna loggar kan lagras i lagring med hjälp av inbyggt **arkivet till ett lagringskonto** alternativet i portalen. Du kan också aktivera lagring med hjälp av en inställning för diagnostik via PowerShell-cmdlets, Azure CLI eller Azure-Monitor REST-API.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schemat för mätvärden och diagnostikfunktionerna loggar i storage-konto

När du har skapat mätvärden och diagnostikfunktionerna loggar samling skapas en lagringsbehållare i storage-konto som du har valt när de första raderna i data är tillgängliga. Strukturen för de här blobbar är:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Eller att:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Till exempel kanske ett blob-namn för alla mått:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Om du vill registrera data från den elastiska poolen är blob-namnet något annat:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Hämta mätvärden och loggar från lagring

Lär dig hur du [hämta mätvärden och diagnostikfunktionerna loggar från lagring](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).

## <a name="metrics-and-logs-available"></a>Mått och tillgängliga loggar

### <a name="all-metrics"></a>Alla mått

|**Resurs**|**Mått**|
|---|---|
|Databas|DTU-procent DTU används, DTU gränsen, CPU-procent, fysiska data skrivskyddade procent loggen skriva procent, lyckade/misslyckade/Blocked av brandväggens anslutningar, sessioner procent, arbetare procent, lagring, lagringsprocent, XTP lagringsprocent och deadlocks |
|Elastisk pool|eDTU procentandel eDTU används, eDTU gränsen, CPU-procent, fysiska data skrivskyddade procent, skriva loggen procent, sessioner procent, arbetare procent, lagring, lagringsprocent, lagringsgräns, XTP-lagringsprocent |
|||

### <a name="query-store-runtime-statistics"></a>Query Store körningsstatistik

|Egenskap|Beskrivning|
|---|---|
|Klient-ID|Klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: QueryStoreRuntimeStatistics|
|OperationName|Namnet på åtgärden. Alltid: QueryStoreRuntimeStatisticsEvent|
|Resurs|Namnet på resursen.|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumeration GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana finns.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|query_hash_s|Fråga hash.|
|query_plan_hash_s|Frågan plan hash.|
|statement_sql_handle_s|Instruktionen sql-referensen.|
|interval_start_time_d|Starta datetimeoffset för intervallet i antal markeringar från 1900-1-1.|
|interval_end_time_d|Avsluta datetimeoffset tidsintervall i antal markeringar från 1900-1-1.|
|logical_io_writes_d|Totalt antal logiska i/o-skrivningar.|
|max_logical_io_writes_d|Högsta antal logiska IO skriver per körning.|
|physical_io_reads_d|Totalt antal fysiska i/o-läsningar.|
|max_physical_io_reads_d|Högsta antal logiska IO läser per körning.|
|logical_io_reads_d|Totalt antal logiska i/o-läsningar.|
|max_logical_io_reads_d|Högsta antal logiska IO läser per körning.|
|execution_type_d|Körningstyp.|
|count_executions_d|Antal körningar av frågan.|
|cpu_time_d|Total CPU-tid som används av frågan i mikrosekunder.|
|max_cpu_time_d|Max CPU tid konsumenten genom en enda körning i mikrosekunder.|
|dop_d|Summan av grader av parallellitet.|
|max_dop_d|Max grad av parallellitet som används för att köra en enda.|
|rowcount_d|Totalt antal rader som returneras.|
|max_rowcount_d|Max antal rader som returneras i samma körning.|
|query_max_used_memory_d|Totala mängden minne som används i KB.|
|max_query_max_used_memory_d|Högsta mängd minne som används av en enda körning i KB.|
|duration_d|Total körningstid i mikrosekunder.|
|max_duration_d|Maximal körningstid för en enda körning.|
|num_physical_io_reads_d|Totalt antal fysiska läsningar.|
|max_num_physical_io_reads_d|Max antal fysiska läsningar per körning.|
|log_bytes_used_d|Totalt antal logg-byte som används.|
|max_log_bytes_used_d|Högsta mängd logg-byte som används per körning.|
|query_id_d|ID för frågan i Frågearkivet.|
|plan_id_d|ID för planen i Query Store.|

Lär dig mer om [Frågearkivet Körningsdata statistik](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Query Store vänta statistik

|Egenskap|Beskrivning|
|---|---|
|Klient-ID|Klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: QueryStoreWaitStatistics|
|OperationName|Namnet på åtgärden. Alltid: QueryStoreWaitStatisticsEvent|
|Resurs|Namn på resursen|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumeration GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana finns.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|wait_category_s|Väntetiden kategori.|
|is_parameterizable_s|Är frågan parameteriseringsbar.|
|statement_type_s|Typ av instruktionen.|
|statement_key_hash_s|Instruktionen nyckel-hash.|
|exec_type_d|Typ av körningen.|
|total_query_wait_time_ms_d|Totalt antal väntetiden för frågan på den specifika vänta kategorin.|
|max_query_wait_time_ms_d|Max väntetid av frågan i enskilda körning i kategorin specifika vänta.|
|query_param_type_d|0|
|query_hash_s|Fråga hash i Query Store.|
|query_plan_hash_s|Frågan plan hash i Query Store.|
|statement_sql_handle_s|Instruktionen referensen i Query Store.|
|interval_start_time_d|Starta datetimeoffset för intervallet i antal markeringar från 1900-1-1.|
|interval_end_time_d|Avsluta datetimeoffset tidsintervall i antal markeringar från 1900-1-1.|
|count_executions_d|Antal körningar av frågan.|
|query_id_d|ID för frågan i Frågearkivet.|
|plan_id_d|ID för planen i Query Store.|

Lär dig mer om [Query Store vänta statistikdata](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Fel dataset

|Egenskap|Beskrivning|
|---|---|
|Klient-ID|Klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: fel|
|OperationName|Namnet på åtgärden. Alltid: ErrorEvent|
|Resurs|Namn på resursen|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumeration GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana finns.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|Meddelande|Felmeddelande i oformaterad text.|
|user_defined_b|Är fel användardefinierade bitar.|
|error_number_d|Felkod.|
|Allvarsgrad|Allvarlighetsgraden för felet.|
|state_d|Status för felet.|
|query_hash_s|Frågan hash för misslyckade frågan, om det är tillgängligt.|
|query_plan_hash_s|Frågan plan hash för misslyckade frågan, om det är tillgängligt.|

Lär dig mer om [felmeddelanden i SQL Server](https://msdn.microsoft.com/en-us/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Databasen vänta statistik dataset

|Egenskap|Beskrivning|
|---|---|
|Klient-ID|Klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: DatabaseWaitStatistics|
|OperationName|Namnet på åtgärden. Alltid: DatabaseWaitStatisticsEvent|
|Resurs|Namn på resursen|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumeration GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana finns.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|wait_type_s|Namnet på typen vänta.|
|start_utc_date_t [UTC]|Mätt periodens starttid.|
|end_utc_date_t [UTC]|Uppmätt period sluttid.|
|delta_max_wait_time_ms_d|Max väntat tid per körning|
|delta_signal_wait_time_ms_d|Totalt antal signal väntetid.|
|delta_wait_time_ms_d|Totalt antal väntetiden under perioden.|
|delta_waiting_tasks_count_d|Antal väntande aktiviteter.|

Lär dig mer om [databasen vänta statistik](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Timeout dataset

|Egenskap|Beskrivning|
|---|---|
|Klient-ID|Klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: timeout|
|OperationName|Namnet på åtgärden. Alltid: TimeoutEvent|
|Resurs|Namn på resursen|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumeration GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana finns.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|error_state_d|Felkod för tillstånd.|
|query_hash_s|Fråga hash, om de är tillgängliga.|
|query_plan_hash_s|Fråga plan hash, om de är tillgängliga.|

### <a name="blockings-dataset"></a>Blockings dataset

|Egenskap|Beskrivning|
|---|---|
|Klient-ID|Klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: block|
|OperationName|Namnet på åtgärden. Alltid: BlockEvent|
|Resurs|Namn på resursen|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumeration GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana finns.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|lock_mode_s|Lås läget som används i frågan.|
|resource_owner_type_s|Ägare till låset.|
|blocked_process_filtered_s|Blockerad rapport XML.|
|duration_d|Varaktighet för låset i mikrosekunder.|

### <a name="intelligent-insights-dataset"></a>Intelligent insikter dataset
Lär dig mer om den [Intelligent insikter loggformatet](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Nästa steg

Läsa information om hur du aktiverar loggning och förstå mått och logga kategorier som stöds av olika Azure-tjänster:

 * [Översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Översikt över Azure diagnostics loggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Läs mer om Händelsehubbar finns:

* [Vad är Händelsehubbar i Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Mer information om lagring finns så [hämta mätvärden och diagnostikfunktionerna loggar från lagring](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).
