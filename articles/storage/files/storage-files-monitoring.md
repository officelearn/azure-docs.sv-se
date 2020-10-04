---
title: Övervaknings Azure Files | Microsoft Docs
description: Lär dig hur du övervakar prestanda och tillgänglighet för Azure Files. Övervaka Azure Files data, lär dig mer om konfiguration och analys av mått-och loggdata.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: e0c5e6041da933b4a42bc438900f8c4c91cc6dbc
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711674"
---
# <a name="monitoring-azure-files"></a>Övervaknings Azure Files

När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervaknings data som genereras av Azure Files och hur du kan använda funktionerna i Azure Monitor för att analysera aviseringar för dessa data.

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. För att registrera dig i för hands versionen, se [den här sidan](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Den här för hands versionen aktiverar loggar för blobbar (som innehåller Azure Data Lake Storage Gen2), filer, köer och tabeller. Den här funktionen är tillgänglig för alla lagrings konton som skapas med Azure Resource Manager distributions modell. Se [Översikt över lagrings konto](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Övervaka översikt

**Översikts** sidan i Azure Portal för varje Azure Files resurs innehåller en kort översikt över resursanvändningen, till exempel begär Anden och fakturering per timme. Den här informationen är användbar, men bara en liten mängd av övervaknings data är tillgänglig. En del av dessa data samlas in automatiskt och är tillgängliga för analys så fort du skapar resursen. Du kan aktivera ytterligare typer av data insamling med en del konfiguration.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?
Azure Files skapar övervaknings data med hjälp av [Azure Monitor](../../azure-monitor/overview.md), som är en fullständig stack övervaknings tjänst i Azure. Azure Monitor innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser och resurser i andra moln och lokalt. 

Börja med artikeln [övervakning av Azure-resurser med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), vilket beskriver följande:

- Vad är Azure Monitor?
- Kostnader för övervakning
- Övervaknings data som samlas in i Azure
- Konfigurerar data insamling
- Standard verktyg i Azure för analys och avisering om övervaknings data

Följande avsnitt bygger på den här artikeln genom att beskriva de specifika data som samlats in från Azure Files. Exempel visar hur du konfigurerar data insamling och analyserar data med Azure-verktyg.

## <a name="monitoring-data"></a>Övervaka data

Azure Files samlar in samma typer av övervaknings data som andra Azure-resurser, som beskrivs i [övervaka data från Azure-resurser](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Se [data referens för Azure File Monitoring](storage-files-monitoring-reference.md) för detaljerad information om mått och loggar mått som skapats av Azure Files.

Mått och loggar i Azure Monitor endast stöd för Azure Resource Manager lagrings konton. Azure Monitor stöder inte klassiska lagrings konton. Om du vill använda mått eller loggar på ett klassiskt lagrings konto måste du migrera till ett Azure Resource Manager lagrings konto. Se [migrera till Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

## <a name="collection-and-routing"></a>Samling och routning

Plattforms mått och aktivitets loggen samlas in automatiskt, men de kan dirigeras till andra platser med hjälp av en diagnostisk inställning. Du måste skapa en diagnostisk inställning för att samla in resurs loggar. 

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. För att registrera dig i för hands versionen, se [den här sidan](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Den här för hands versionen aktiverar loggar för blobbar (som innehåller Azure Data Lake Storage Gen2), filer, köer, tabeller, Premium Storage-konton i generell användning v1 och lagrings konton för generell användning v2. Klassiska lagrings konton stöds inte.

Om du vill skapa en diagnostisk inställning med hjälp av Azure Portal, Azure CLI eller PowerShell, se [skapa diagnostisk inställning för att samla in plattforms loggar och mått i Azure](../../azure-monitor/platform/diagnostic-settings.md). 

Om du vill se en Azure Resource Manager mall som skapar en diagnostisk inställning, se [diagnostisk inställning för Azure Storage](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage).

När du skapar en diagnostisk inställning väljer du den typ av lagring som du vill aktivera loggar för, till exempel en BLOB, kö, tabell eller fil. För Azure Files väljer du **fil**. 

Om du skapar en diagnostisk inställning i Azure Portal kan du välja resursen från en lista. Om du använder PowerShell eller Azure CLI måste du använda resurs-ID: t för Azure Files slut punkten. Du hittar resurs-ID: t i Azure Portal genom att öppna sidan **Egenskaper** för ditt lagrings konto.

Du måste också ange en av följande kategorier av åtgärder som du vill samla in loggar för. 

| Kategori | Beskrivning |
|:---|:---|
| StorageRead | Läs åtgärder för objekt. |
| StorageWrite | Skriv åtgärder för objekt. |
| StorageDelete | Ta bort åtgärder för objekt. |

## <a name="analyzing-metrics"></a>Analyserar mått

Du kan analysera mått för Azure Storage med mått från andra Azure-tjänster med hjälp av Metrics Explorer. Öppna Metrics Explorer genom att välja **mått** på **Azure Monitor** -menyn. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). 

För mått som stöder dimensioner kan du filtrera måttet med det önskade dimension svärdet.  En fullständig lista över de dimensioner som Azure Storage stöder finns i [mått dimensioner](storage-files-monitoring-reference.md#metrics-dimensions). Mått för Azure Files finns i följande namnrymder: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/fileServices

För en lista över alla Azure Monitor-support-mått, som innehåller Azure Files, se [Azure Monitor mått som stöds](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Åtkomst till mått

> [!TIP]
> Om du vill visa Azure CLI-eller .NET-exempel väljer du de motsvarande flikarna som visas här.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Lista mått definitionen

Du kan visa mått definitionen för ditt lagrings konto eller tjänsten Azure Files. Använd cmdleten [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition) .

I det här exemplet ersätter du `<resource-ID>` plats hållaren med resurs-ID för hela lagrings kontot eller resurs-ID: t för den Azure Files tjänsten.  Du hittar dessa resurs-ID: n på **egenskaps** sidorna för ditt lagrings konto i Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Läser mått värden

Du kan läsa mått värden på konto nivå för ditt lagrings konto eller tjänsten Azure Files. Använd cmdleten [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Lista mått definitionen på konto nivå

Du kan visa mått definitionen för ditt lagrings konto eller tjänsten Azure Files. Använd kommandot [AZ Monitor Metric List-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) .
 
I det här exemplet ersätter du `<resource-ID>` plats hållaren med resurs-ID för hela lagrings kontot eller resurs-ID: t för den Azure Files tjänsten. Du hittar dessa resurs-ID: n på **egenskaps** sidorna för ditt lagrings konto i Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Läs mått värden på konto nivå

Du kan läsa mått värden för ditt lagrings konto eller tjänsten Azure Files. Använd kommandot [AZ Monitor Metric List](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor tillhandahåller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) för att läsa mått definition och-värden. [Exempel koden](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) visar hur du använder SDK med olika parametrar. Du måste använda `0.18.0-preview` eller en senare version för lagrings mått.
 
I de här exemplen ersätter du `<resource-ID>` plats hållaren med resurs-ID för hela lagrings kontot eller tjänsten Azure Files. Du hittar dessa resurs-ID: n på **egenskaps** sidorna för ditt lagrings konto i Azure Portal.

Ersätt `<subscription-ID>` variabeln med ID: t för din prenumeration. Anvisningar för hur du hämtar värden för `<tenant-ID>` , `<application-ID>` och `<AccessKey>` finns i [använda portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-the-account-level-metric-definition"></a>Lista mått definitionen på konto nivå

I följande exempel visas hur du listar en mått definition på konto nivån:

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

#### <a name="reading-account-level-metric-values"></a>Läser mått värden på konto nivå

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

#### <a name="reading-multidimensional-metric-values"></a>Läser flerdimensionella mått värden

För flerdimensionella mått måste du definiera metadata-filter om du vill läsa mått data för vissa dimensions värden.

I följande exempel visas hur du läser mått data för måttet som stöder multidimension:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
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

## <a name="analyzing-logs"></a>Analysera loggar

Du kan komma åt resurs loggar antingen som en BLOB i ett lagrings konto, som händelse data eller genom att logga analys frågor.

En detaljerad referens för de fält som visas i dessa loggar finns i [referens för Azure Azure Files övervaknings data](storage-files-monitoring-reference.md).

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. För att registrera dig i för hands versionen, se [den här sidan](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Den här för hands versionen aktiverar loggar för blobbar (som innehåller Azure Data Lake Storage Gen2), filer, köer, tabeller, Premium Storage-konton i generell användning v1 och lagrings konton för generell användning v2. Klassiska lagrings konton stöds inte.

Logg poster skapas endast om det finns begär Anden som görs mot tjänst slut punkten. Om ett lagrings konto till exempel har aktivitet i dess fil slut punkt men inte i dess tabell-eller Queue-slutpunkter skapas bara loggar som hör till fil tjänsten. Azure Storage loggar innehåller detaljerad information om lyckade och misslyckade förfrågningar till en lagrings tjänst. Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Begär Anden loggas med bästa möjliga ansträngning.

### <a name="log-authenticated-requests"></a>Logga autentiserade begär Anden

 Följande typer av autentiserade begäranden loggas:

- Lyckade begäranden
- Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel
- Begär Anden som använder en signatur för delad åtkomst (SAS) eller OAuth, inklusive misslyckade och lyckade förfrågningar
- Begär Anden om analys av data (klassiska loggdata i **$logs** container-och klass mått data i **$Metric** tabeller)

Begär Anden som görs av själva tjänsten Azure Files, till exempel när loggen skapas eller tas bort, loggas inte. En fullständig lista över de loggade data som loggas finns i [lagrings loggade åtgärder och status meddelanden](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [logg format för lagring](storage-files-monitoring-reference.md).

### <a name="log-anonymous-requests"></a>Logga anonyma begär Anden

 Följande typer av anonyma begär Anden loggas:

- Lyckade begäranden
- Serverfel
- Timeoutfel för både klient och server
- Misslyckade GET-begäranden med felkoden 304 (inte ändrad)

Alla andra misslyckade anonyma begär Anden loggas inte. En fullständig lista över de loggade data som loggas finns i [lagrings loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [logg format för lagring](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Åtkomst till loggar i ett lagrings konto

Loggar visas som blobbar som lagras i en behållare på mål lagrings kontot. Data samlas in och lagras i en enda blob som en linje avgränsad JSON-nyttolast. Namnet på blobben följer denna namngivnings konvention:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Här är ett exempel:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Komma åt loggar i en Event Hub

Loggar som skickas till en Event Hub lagras inte som en fil, men du kan kontrol lera att händelsehubben har fått logg informationen. I Azure Portal går du till händelsehubben och kontrollerar att antalet **inkommande meddelanden** är större än noll. 

![Granskningsloggar](media/storage-files-monitoring/event-hub-log.png)

Du kan komma åt och läsa loggdata som skickas till händelsehubben med hjälp av säkerhets information och verktyg för händelse hantering och övervakning. Mer information finns i [vad kan jag göra med övervaknings data som skickas till min Event Hub?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Komma åt loggar i en Log Analytics-arbetsyta

Du kan komma åt loggar som skickas till en Log Analytics arbets yta genom att använda Azure Monitor logg frågor. Data lagras i tabellen **StorageFileLogs** . 

Mer information finns i [Kom igång med Log Analytics i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

#### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Här följer några frågor som du kan ange i **logg Sök** fältet för att hjälpa dig att övervaka din Azure Files. Dessa frågor fungerar med det [nya språket](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

> [!IMPORTANT]
> När du väljer **loggar** från menyn resurs grupp för lagrings konto öppnas Log Analytics med fråge omfånget som är inställt på den aktuella resurs gruppen. Det innebär att logg frågor bara innehåller data från den resurs gruppen. Om du vill köra en fråga som innehåller data från andra resurser eller data från andra Azure-tjänster väljer du **loggar** på **Azure Monitor** -menyn. Mer information finns i [logg frågans omfång och tidsintervall i Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) .

Använd dessa frågor för att övervaka dina Azure-filresurser:

- Visa SMB-fel under den senaste veckan

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Skapa ett cirkel diagram med SMB-åtgärder under den senaste veckan

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Visa REST-fel under den senaste veckan

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Skapa ett cirkel diagram med REST-åtgärder under den senaste veckan

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Om du vill visa en lista över kolumn namn och beskrivningar för Azure Files, se [StorageFileLogs](https://docs.microsoft.com/azure/azure-monitor/reference/tables/storagefilelogs).

Mer information om hur du skriver frågor finns i [Självstudier: kom igång med Log Analytics frågor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

## <a name="alerts"></a>Aviseringar

Azure Monitor aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem i systemet innan kunderna märker dem. Du kan ställa in aviseringar för [mått](/azure/azure-monitor/platform/alerts-metric-overview), [loggar](/azure/azure-monitor/platform/alerts-unified-log)och [aktivitets loggen](/azure/azure-monitor/platform/activity-log-alerts). 

I följande tabell visas några exempel scenarier som du kan använda för att övervaka och rätt mått för aviseringen:

| Scenario | Mått som ska användas för avisering |
|-|-|
| Fil resursen är begränsad. | Mått: transaktioner<br>Dimensions namn: svarstyp <br>Dimensions namn: fil resursen (endast Premium-filresurs) |
| Fil resursens storlek är 80% av kapaciteten. | Mått: fil kapacitet<br>Dimensions namn: fil resursen (endast Premium-filresurs) |
| Den utgående fil resursen har överskridit 500 GiB på en dag. | Mått: utgående<br>Dimensions namn: fil resursen (endast Premium-filresurs) |

### <a name="how-to-create-alerts-for-azure-files"></a>Skapa aviseringar för Azure Files

1. Gå till ditt **lagrings konto** i **Azure Portal**. 

2. Klicka på **aviseringar** och klicka sedan på **+ ny varnings regel**.

3. Klicka på **Redigera resurs**, Välj **fil resurs typ** och klicka sedan på **färdig**. 

4. Klicka på **Välj villkor** och ange följande information för aviseringen: 

    - **Mått**
    - **Dimensions namn**
    - **Aviserings logik**

5. Klicka på **Välj åtgärds grupp** och Lägg till en åtgärds grupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.

6. Fyll i **aviserings informationen** som **aviserings regelns namn**, **Beskrivning**och **allvarlighets grad**.

7. Klicka på **skapa aviserings regel** för att skapa aviseringen.

> [!NOTE]  
> Om du skapar en avisering och det är för brus, justera tröskelvärdet och aviserings logiken.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Så här skapar du en avisering om en fil resurs är begränsad

1. Gå till ditt **lagrings konto** i **Azure Portal**.
2. I avsnittet **övervakning** klickar du på **aviseringar**och klickar sedan på **+ ny varnings regel**.
3. Klicka på **Redigera resurs**, Välj **fil resurs typ** för lagrings kontot och klicka sedan på **färdig**. Om lagrings kontots namn till exempel är `contoso` väljer du `contoso/file` resursen.
4. Klicka på **Välj villkor** för att lägga till ett villkor.
5. Du kommer att se en lista över signaler som stöds för lagrings kontot. Välj måttet **transaktioner** .
6. På bladet **Konfigurera signal logik** klickar du på list rutan **Dimensions namn** och väljer **svarstyp**.
7. Klicka på list rutan **Dimensions värden** och välj **SUCCESSWITHTHROTTLING** (för SMB) eller **ClientThrottlingError** (för rest).

   > [!NOTE]
   > Om dimension svärdet SuccessWithThrottling eller ClientThrottlingError inte visas innebär det att resursen inte har begränsats. Lägg till dimension svärdet genom att klicka på **Lägg till anpassat värde** bredvid List rutan **Dimensions värden** , Skriv **SuccessWithThrottling** eller **ClientThrottlingError**, klicka på **OK** och upprepa steg #7.

8. Klicka på list rutan **Dimensions namn** och välj **fil resurs**.
9. Klicka på list rutan **Dimensions värden** och välj den eller de fil resurser som du vill Avisera om.

   > [!NOTE]
   > Om fil resursen är en standard fil resurs väljer du **alla aktuella och framtida värden**. List rutan med dimensions värden visar inte fil resurserna eftersom det inte finns några tillgängliga fil resurser per resurs. Begränsnings varningar för standard fil resurser utlöses om någon fil resurs på lagrings kontot är begränsad och aviseringen inte kommer att identifiera vilken fil resurs som har begränsats. Eftersom per resurs-mått inte är tillgängliga för standard fil resurser, är rekommendationen att ha en fil resurs per lagrings konto.

10. Definiera **aviserings parametrarna** (tröskelvärde, Operator, agg regerings precision och frekvens för utvärderingen) och klicka på **Slutför**.

    > [!TIP]
    > Om du använder ett statiskt tröskelvärde kan mått diagrammet hjälpa till att fastställa ett rimligt tröskelvärde om fil resursen för närvarande begränsas. Om du använder ett dynamiskt tröskelvärde visar mått diagrammet de beräknade tröskelvärdena baserat på aktuella data.

11. Klicka på **Välj åtgärds grupp** för att lägga till en **Åtgärds grupp** (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
12. Fyll i **aviserings informationen** som **aviserings regelns namn**, * * beskrivning och **allvarlighets grad**.
13. Klicka på **skapa aviserings regel** för att skapa aviseringen.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Så här skapar du en avisering om storleken på Azure-filresursen är 80% av kapaciteten

1. Gå till ditt **lagrings konto** i **Azure Portal**.
2. I avsnittet **övervakning** klickar du på **aviseringar** och klickar sedan på **+ ny varnings regel**.
3. Klicka på **Redigera resurs**, Välj **fil resurs typ** för lagrings kontot och klicka sedan på **färdig**. Om lagrings kontots namn till exempel är `contoso` väljer du `contoso/file` resursen.
4. Klicka på **Välj villkor** för att lägga till ett villkor.
5. Du ser en lista över signaler som stöds för lagrings kontot genom att välja **fil kapacitets** mått.
6. På bladet **Konfigurera signal logik** klickar du på list rutan **Dimensions namn** och väljer **fil resurs**.
7. Klicka på list rutan **Dimensions värden** och välj den eller de fil resurser som du vill Avisera om.

   > [!NOTE]
   > Om fil resursen är en standard fil resurs väljer du **alla aktuella och framtida värden**. List rutan med dimensions värden visar inte fil resurserna eftersom det inte finns några tillgängliga fil resurser per resurs. Aviseringar för standard fil resurser baseras på alla fil resurser i lagrings kontot. Eftersom per resurs-mått inte är tillgängliga för standard fil resurser, är rekommendationen att ha en fil resurs per lagrings konto.

8. Ange **tröskelvärdet** i byte. Om fil resursens storlek exempelvis är 100 TiB och du vill få en avisering när fil resurs storleken är 80% av kapaciteten, är tröskelvärdet i byte 87960930222080.
9. Definiera resten av **aviserings parametrarna** (sammansättnings precision och utvärderings frekvens) och klicka på **Slutför**.
10. Klicka på Välj åtgärds grupp för att lägga till en åtgärds grupp (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
11. Fyll i **aviserings informationen** som **aviserings regelns namn**, * * beskrivning och **allvarlighets grad**.
12. Klicka på **skapa aviserings regel** för att skapa aviseringen.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Så här skapar du en avisering om utgående Azure-filresurs har överskridit 500 GiB per dag

1. Gå till ditt **lagrings konto** i **Azure Portal**.
2. I avsnittet övervakning klickar du på **aviseringar** och klickar sedan på **+ ny varnings regel**.
3. Klicka på **Redigera resurs**, Välj **fil resurs typ** för lagrings kontot och klicka sedan på **färdig**. Om lagrings konto namnet till exempel är contoso väljer du Contoso/File-resursen.
4. Klicka på **Välj villkor** för att lägga till ett villkor.
5. Du kommer att se en lista över signaler som stöds för lagrings kontot och välja **utgående** mått.
6. På bladet **Konfigurera signal logik** klickar du på list rutan **Dimensions namn** och väljer **fil resurs**.
7. Klicka på list rutan **Dimensions värden** och välj den eller de fil resurser som du vill Avisera om.

   > [!NOTE]
   > Om fil resursen är en standard fil resurs väljer du **alla aktuella och framtida värden**. List rutan med dimensions värden visar inte fil resurserna eftersom det inte finns några tillgängliga fil resurser per resurs. Aviseringar för standard fil resurser baseras på alla fil resurser i lagrings kontot. Eftersom per resurs-mått inte är tillgängliga för standard fil resurser, är rekommendationen att ha en fil resurs per lagrings konto.

8. Ange **536870912000** byte för tröskel värde. 
9. Klicka på list rutan **agg regerings granularitet** och välj **24 timmar**.
10. Välj **utvärderings frekvens** och **Klicka på Slutför**.
11. Klicka på **Välj åtgärds grupp** för att lägga till en **Åtgärds grupp** (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
12. Fyll i **aviserings informationen** som **aviserings regelns namn**, * * beskrivning och **allvarlighets grad**.
13. Klicka på **skapa aviserings regel** för att skapa aviseringen.

## <a name="next-steps"></a>Nästa steg

- [Azure Files övervaknings data referens](storage-files-monitoring.md)
- [Övervaka Azure-resurser med Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md)
- [Migrering av Azure Storage mått](../common/storage-metrics-migration.md)
- [Planera för en Azure Files-distribution](https://docs.microsoft.com/azure/storage/files/storage-files-planning)
- [Så här distribuerar du Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)
- [Felsöka Azure Files på Windows](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)
- [Felsöka Azure Files på Linux](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-linux-file-connection-problems)
