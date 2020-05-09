---
title: Övervaknings Azure Storage | Microsoft Docs
description: Lär dig hur du övervakar prestanda och tillgänglighet för Azure Storage.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5564634471045838dae3344dc883b6fdc203711e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722926"
---
# <a name="monitoring-azure-storage"></a>Övervaknings Azure Storage

När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervaknings data som genereras av Azure Storage och hur du kan använda funktionerna i Azure Monitor för att analysera aviseringen om dessa data.

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. För att registrera dig i för hands versionen, se [den här sidan](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Den här för hands versionen aktiverar loggar för blobbar (inklusive Azure Data Lake Storage Gen2), filer, köer, tabeller, Premium Storage-konton i generell användning v1-och General-Purpose v2-lagrings konton. Klassiska lagrings konton stöds inte.

## <a name="monitor-overview"></a>Övervaka översikt

**Översikts** sidan i Azure Portal för varje lagrings resurs innehåller en kort översikt över resursanvändningen, inklusive begäran och fakturerings användningen per timme. Detta är användbar information, men bara en liten mängd tillgängliga övervaknings data. En del av dessa data samlas in automatiskt och är tillgänglig för analys så fort du skapar lagrings resursen. Du kan aktivera ytterligare typer av data insamling med en del konfiguration.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
Azure Storage skapar övervaknings data med hjälp av [Azure Monitor](../../azure-monitor/overview.md) som är en fullständig stack övervaknings tjänst i Azure som innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser, förutom resurser i andra moln och lokalt. 

Börja med artikeln [övervakning av Azure-resurser med Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) som beskriver följande:

- Vad är Azure Monitor?
- Kostnader för övervakning
- Övervaknings data som samlas in i Azure
- Konfigurerar data insamling
- Standard verktyg i Azure för analys och avisering om övervaknings data

Följande avsnitt bygger på den här artikeln genom att beskriva de specifika data som samlats in från Azure Storage och tillhandahålla exempel för att konfigurera data insamling och analysera data med Azure-verktyg.

## <a name="monitoring-data-from-azure-storage"></a>Övervaknings data från Azure Storage

Azure Storage samlar in samma typer av övervaknings data som andra Azure-resurser som beskrivs i [övervaknings data från Azure-resurser](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Se [Azure Storage övervaknings data referens](monitor-storage-reference.md) för en detaljerad referens för de loggar och mått som skapats av Azure Storage.

Mått och loggar i Azure Monitor endast stöd för Azure Resource Manager lagrings konton. Azure Monitor stöder inte klassiska lagrings konton. Om du vill använda mått eller loggar på ett klassiskt lagrings konto måste du migrera till Azure Resource Manager lagrings konto. Se [migrera till Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Du kan fortsätta använda klassiska mått och loggar om du vill. Faktum är att klassiska mått och loggar är tillgängliga parallellt med mått och loggar i Azure Monitor. Stödet finns kvar tills Azure Storage avslutar tjänsten med äldre mått och loggar. 

### <a name="logs-in-azure-monitor-preview"></a>Loggar i Azure Monitor (för hands version)

Logg poster skapas endast om det finns begär Anden som görs mot tjänst slut punkten. Om ett lagrings konto till exempel har aktivitet i dess BLOB-slutpunkt men inte i dess tabell-eller Queue-slutpunkter, skapas endast loggar som rör BLOB service. Azure Storage loggar innehåller detaljerad information om lyckade och misslyckade förfrågningar till en lagrings tjänst. Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Begär Anden loggas med bästa möjliga ansträngning.

#### <a name="logging-authenticated-requests"></a>Logga autentiserade begär Anden

 Följande typer av autentiserade begäranden loggas:

- Lyckade begäranden
- Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel
- Begäranden med en signatur för delad åtkomst (SAS), inklusive misslyckade och lyckade begäranden
- Begär Anden om analys av data (klassiska loggdata i **$logs** container och klass mått data i **$Metric** tabeller)

Begär Anden som görs av själva lagrings tjänsten, till exempel när loggen skapas eller tas bort, loggas inte. En fullständig lista över de loggade data som dokumenteras finns i avsnitten [lagrings loggar och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [logg format för lagring](monitor-storage-reference.md) .

#### <a name="logging-anonymous-requests"></a>Logga anonyma begär Anden

 Följande typer av anonyma begär Anden loggas:

- Lyckade begäranden
- Serverfel
- Timeout-fel för både klienten och servern
- Misslyckade GET-begäranden med felkoden 304 (har inte ändrats)

Alla andra misslyckade anonyma begär Anden loggas inte. En fullständig lista över de loggade data som dokumenteras finns i avsnitten [lagrings loggar och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [logg format för lagring](monitor-storage-reference.md) .

## <a name="configuration"></a>Konfiguration

Plattforms mått och aktivitets loggen samlas in automatiskt, men du måste skapa en diagnostisk inställning för att samla in resurs loggar eller vidarebefordra dem utanför Azure Monitor. Mer information om hur du skapar en diagnostisk inställning med hjälp av Azure Portal, CLI eller PowerShell finns i [skapa diagnostisk inställning för att samla in plattforms loggar och statistik i Azure](../../azure-monitor/platform/diagnostic-settings.md) .

När du skapar en diagnostisk inställning måste du välja den typ av lagring som du vill aktivera loggar för (BLOB, kö, tabell, fil). Om du skapar den diagnostiska inställningen i Azure Portal kan du välja resursen från en lista. Om du använder PowerShell eller Azure CLI måste du använda resurs-ID för lagrings typen. Du hittar resurs-ID: t i Azure Portal genom att öppna sidan **Egenskaper** för ditt lagrings konto.

Du måste också ange vilka kategorier av åtgärder som ska samla in loggar för. Kategorierna för Azure Storage visas i följande tabell:

| Kategori | Beskrivning |
|:---|:---|
| StorageRead | Läs åtgärder på blobbar.  |
| StorageWrite | Skriv åtgärder på blobbar. |
| StorageDelete | Ta bort åtgärder på blobbar. |

## <a name="analyzing-metric-data"></a>Analysera mått data

Du kan analysera mått för Azure Storage med mått från andra Azure-tjänster med hjälp av Metrics Explorer. Öppna Metrics Explorer genom att välja **mått** på **Azure Monitor** -menyn. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md) . 

I följande exempel visas hur du visar **transaktioner** på konto nivå.

![skärm bild av åtkomst till mått i Azure Portal](./media/monitor-storage/access-metrics-portal.png)

För mått som stöder dimensioner kan du filtrera måttet med det önskade dimension svärdet. I följande exempel visas hur du visar **transaktioner** på konto nivå för en speciell åtgärd genom att välja värden för dimensionen **API-namn** .

![skärm bild av åtkomst till mått med dimension i Azure Portal](./media/monitor-storage/access-metrics-portal-with-dimension.png)

En fullständig lista över de dimensioner som Azure Storage stöder finns i [mått dimensioner](monitor-storage-reference.md#metrics-dimensions).

Alla mått för Azure Storage finns i följande namnrymder:

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/blobServices
- Microsoft. Storage/storageAccounts/fileServices
- Microsoft. Storage/storageAccounts/queueServices
- Microsoft. Storage/storageAccounts/tableServices

En lista över alla Azure Monitor support mått (inklusive Azure Storage) finns i [Azure Monitor mått som stöds](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="access-metrics"></a>Åtkomst mått

> [!TIP]
> Om du vill visa Azure CLI-eller .NET-exempel väljer du motsvarande flik nedan.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Lista mått definitionen

Du kan visa mått definitionen för ditt lagrings konto, eller den enskilda lagrings tjänsten som BLOB, fil, tabell eller Queue Service. Använd cmdleten [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) .

I det här exemplet ersätter du `<resource-ID>` plats hållaren med resurs-ID för hela lagrings kontot eller resurs-ID för en enskild lagrings tjänst, till exempel som BLOB, fil, tabell eller Queue Service. Du kan hitta dessa resurs-ID: n på **egenskaps** sidorna för ditt lagrings konto på Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Läs mått värden

Du kan läsa mått värden på konto nivå för ditt lagrings konto, eller den enskilda lagrings tjänsten som BLOB, fil, tabell eller Queue Service. Använd cmdleten [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-account-level-metric-definition"></a>Lista mått definition för konto nivå

Du kan visa mått definitionen för ditt lagrings konto, eller den enskilda lagrings tjänsten som BLOB, fil, tabell eller Queue Service. Använd kommandot [AZ Monitor Metric List-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) .
 
I det här exemplet ersätter du `<resource-ID>` plats hållaren med resurs-ID för hela lagrings kontot eller resurs-ID för en enskild lagrings tjänst, till exempel som BLOB, fil, tabell eller Queue Service. Du kan hitta dessa resurs-ID: n på **egenskaps** sidorna för ditt lagrings konto på Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Läs mått värden på konto nivå

Du kan läsa mått värden för ditt lagrings konto eller den enskilda lagrings tjänsten, till exempel BLOB, fil, tabell eller Queue Service. Använd kommandot [AZ Monitor Metric List](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor tillhandahåller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) för att läsa mått definition och-värden. [Exempel koden](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) visar hur du använder SDK med olika parametrar. Du måste använda `0.18.0-preview` eller en senare version för lagrings mått.
 
I de här exemplen ersätter `<resource-ID>` du plats hållaren med resurs-ID för hela lagrings kontot, eller resurs-ID för en enskild lagrings tjänst, till exempel som BLOB-, fil-, tabell-eller Queue-tjänst. Du kan hitta dessa resurs-ID: n på **egenskaps** sidorna för ditt lagrings konto på Azure Portal.

Ersätt `<subscription-ID>` variabeln med ID: t för din prenumeration.  Anvisningar för hur du hämtar värden `<tenant-ID>`för, `<application-ID>`och `<AccessKey>`finns i [How to: använda portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-account-level-metric-definition"></a>Lista mått definition för konto nivå

I följande exempel visas hur du listar mått definition på konto nivå:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="read-account-level-metric-values"></a>Läs mått värden på konto nivå

I följande exempel visas hur du läser `UsedCapacity` data på konto nivå:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="read-multi-dimensional-metric-values"></a>Läs multi-dimensionella mått värden

För flerdimensionella mått måste du definiera meta data filter om du vill läsa mått data för ett angivet dimensions värde.

I följande exempel visas hur du läser mått data för måttet som stöder flera dimensioner:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

---

## <a name="analyzing-log-data"></a>Analysera loggdata

Du kan komma åt resurs loggar antingen som en BLOB i ett lagrings konto, som händelse data eller genom att logga analys frågor.

Se [Azure Storage övervaknings data referens](monitor-storage-reference.md) för en detaljerad referens för de fält som visas i dessa loggar.

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. För att registrera dig i för hands versionen, se [den här sidan](https://www.microsoft.com).  Den här för hands versionen aktiverar loggar för blobbar (inklusive Azure Data Lake Storage Gen2), filer, köer, tabeller, Premium Storage-konton i generell användning v1-och General-Purpose v2-lagrings konton. Klassiska lagrings konton stöds inte.

### <a name="access-logs-in-a-storage-account"></a>Åtkomst till loggar i ett lagrings konto

Loggar visas som blobbar som lagras i en behållare på mål lagrings kontot. Data samlas in och lagras i en enda blob som en begränsad JSON-nyttolast. Namnet på blobben följer följande namngivnings konvention:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Här är ett exempel:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-event-hub"></a>Åtkomst loggar i Händelsehubben

Loggar som skickas till en Event Hub lagras inte som en fil, men du kan kontrol lera att händelsehubben har fått logg informationen. I Azure Portal navigerar du till händelsehubben och kontrollerar att antalet **inkommande meddelanden** är större än noll. 

![Granskningsloggar](media/monitor-storage/event-hub-log.png)

Du kan komma åt och läsa loggdata som skickas till händelsehubben med hjälp av säkerhets information och verktyg för händelse hantering och övervakning. Mer information finns i [vad kan jag göra med övervaknings data som skickas till min Event Hub?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="access-logs-in-log-analytics-workspace"></a>Åtkomst till loggar i Log Analytics-arbetsyta

Du kan komma åt loggar som skickas till en Log Analytics arbets yta genom att använda Azure Monitor logg frågor.

Se [Kom igång med Log Analytics i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Data lagras i följande tabeller.

| Tabell | Beskrivning |
|:---|:---|
|StorageBlobLogs | Loggar som beskriver aktivitet i Blob Storage. |
|StorageFileLogs | Loggar som beskriver aktivitet i fil resurser. |
|StorageQueueLogs | Loggar som beskriver aktivitet i köer.|
|StorageTableLogs| Loggar som beskriver aktivitet i tabeller.|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Azure Storage Log Analytics frågor i Azure Monitor

Här följer några frågor som du kan ange i Sök fältet för **loggs ökning** och som hjälper dig att övervaka dina Azure Storage-konton. Dessa frågor fungerar med det [nya språket](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Följande är frågor som du kan använda för att övervaka dina Azure Storage-konton.

* Visa 10 vanligaste felen de senaste 3 dagarna.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* För att lista de 10 vanligaste åtgärderna som orsakar flest fel under de senaste tre dagarna.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* För att lista de 10 främsta operationerna med längsta slut tid till slut tid under de senaste tre dagarna.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Om du vill visa alla åtgärder som orsakar begränsning på Server sidan under de senaste 3 dagarna.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* För att visa en lista över alla begär Anden med anonym åtkomst under de senaste tre dagarna.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* För att skapa ett cirkel diagram med åtgärder som använts under de senaste 3 dagarna.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Stöder Azure Storage mått för Managed Disks eller ohanterade diskar?**

Nej, Azure Compute stöder mått på diskar. Mer information finns i [artikeln](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) .

## <a name="next-steps"></a>Nästa steg

- [Azure Storage övervaknings data referens](monitor-storage-reference.md) för en referens till de loggar och mått som skapats av Azure Storage.
- [Övervaka Azure-resurser med Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) för information om övervakning av Azure-resurser.
- [Migrering av Azure Storage mått](./storage-metrics-migration.md)

