---
title: Azure Storage mått i Azure Monitor | Microsoft Docs
description: Lär dig mer om de nya mått som erbjuds från Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 62e2e3f1a80cef04dc778d5a1950cca97d79dcb0
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748375"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure Storage-mått i Azure Monitor

Med mått på Azure Storage kan du analysera användnings trender, spåra begär Anden och diagnostisera problem med ditt lagrings konto.

Azure Monitor tillhandahåller enhetliga användar gränssnitt för övervakning i olika Azure-tjänster. Mer information finns i [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Azure Storage integrerar Azure Monitor genom att skicka mått data till Azure Monitors plattformen.

## <a name="access-metrics"></a>Åtkomst-mått

Azure Monitor innehåller flera sätt att åtkomst mått. Du kan komma åt dem från [Azure Portal](https://portal.azure.com), Azure Monitors-API: er (rest och .net) och analys lösningar som Event Hubs. Mer information finns i [Azure Monitor mått](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Mått är aktiverade som standard och du kan komma åt de senaste 93 dagarna med data. Om du vill behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta är konfigurerat i [diagnostikinställningar](../../azure-monitor/platform/platform-logs-overview.md) i Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Åtkomst mått i Azure Portal

Du kan övervaka mått över tid i Azure Portal. I följande exempel visas hur du visar **transaktioner** på konto nivå.

![skärm bild av åtkomst till mått i Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

För mått som stöder dimensioner kan du filtrera måttet med det önskade dimension svärdet. I följande exempel visas hur du visar **transaktioner** på konto nivå för en speciell åtgärd genom att välja värden för dimensionen **API-namn** .

![skärm bild av åtkomst till mått med dimension i Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Åtkomst mått med REST API

Azure Monitor tillhandahåller [REST-API: er](/rest/api/monitor/) för att läsa mått definition och värden. I det här avsnittet visas hur du läser lagrings måtten. Resurs-ID används i alla REST-API: er. Mer information finns i förstå resurs-ID för tjänster i lagrings utrymmen.

I följande exempel visas hur du använder [ArmClient](https://github.com/projectkudu/ARMClient) på kommando raden för att förenkla testningen med REST API.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Lista konto nivå mått definition med REST API

I följande exempel visas hur du listar mått definition på konto nivå:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Om du vill visa mått definitionerna för BLOB, tabell, fil eller kö måste du ange olika resurs-ID: n för varje tjänst med API: et.

Svaret innehåller mått definitionen i JSON-format:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Läs mått värden på konto nivå med REST API

I följande exempel visas hur du läser mått data på konto nivå:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

I ovanstående exempel, om du vill läsa mått värden för BLOB, tabell, fil eller kö, måste du ange olika resurs-ID: n för varje tjänst med API: et.

Följande svar innehåller mått värden i JSON-format:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Åtkomst mått med .NET SDK

Azure Monitor tillhandahåller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) för att läsa mått definition och-värden. [Exempel koden](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) visar hur du använder SDK med olika parametrar. Du måste använda `0.18.0-preview` eller en senare version för lagrings mått. Resurs-ID används i .NET SDK. Mer information finns i förstå resurs-ID för tjänster i lagrings utrymmen.

I följande exempel visas hur du använder Azure Monitor .NET SDK för att läsa lagrings mått.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Lista konto nivå mått definition med .NET SDK

I följande exempel visas hur du listar mått definition på konto nivå:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
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

Om du vill visa mått definitionerna för BLOB, tabell, fil eller kö måste du ange olika resurs-ID: n för varje tjänst med API: et.

#### <a name="read-metric-values-with-the-net-sdk"></a>Läsa Metric-värden med .NET SDK

I följande exempel visas hur du läser `UsedCapacity` data på konto nivå:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

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

I ovanstående exempel, om du vill läsa mått värden för BLOB, tabell, fil eller kö, måste du ange olika resurs-ID: n för varje tjänst med API: et.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Läs multi-dimensionella mått värden med .NET SDK

För flerdimensionella mått måste du definiera meta data filter om du vill läsa mått data för ett angivet dimensions värde.

I följande exempel visas hur du läser mått data för måttet som stöder flera dimensioner:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Förstå resurs-ID för tjänster i Azure Storage

Resurs-ID är en unik identifierare för en resurs i Azure. När du använder Azure Monitor REST API för att läsa mått definitioner eller värden måste du använda resurs-ID för den resurs som du vill använda. Mallen resurs-ID följer detta format:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Storage tillhandahåller mått på både lagrings konto nivå och service nivå med Azure Monitor. Du kan till exempel hämta mått för precis Blob Storage. Varje nivå har sitt eget resurs-ID, som används för att hämta måtten för just den nivån.

### <a name="resource-id-for-a-storage-account"></a>Resurs-ID för ett lagrings konto

Nedan visas formatet för att ange resurs-ID för ett lagrings konto.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>Resurs-ID för lagrings tjänsterna

Nedan visas formatet för att ange resurs-ID för var och en av lagrings tjänsterna.

* Resurs-ID för Blob Service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Resurs-ID för Table service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Resurs-ID för Kötjänst
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Resurs-ID för fil tjänst
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Resurs-ID i Azure Monitor REST API

Nedan visas mönstret som används när du anropar Azure Monitor REST API.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Kapacitetsmått
Värdena för kapacitets värden skickas till Azure Monitor varje timme. Värdena uppdateras varje dag. Tids kornig het definierar tidsintervallet för vilka mått värden presenteras. Tids kornig het för alla kapacitets mått är en timme (PT1H).

Azure Storage tillhandahåller följande kapacitets mått i Azure Monitor.

### <a name="account-level"></a>Konto nivå

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| UsedCapacity | Mängden lagrings utrymme som används av lagrings kontot. För standardlagringskonton är det summan av den kapacitet som används av blobar, filer och köer. För Premium-lagringskonton och Blob Storage-konton är det samma sak som BlobCapacity. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

### <a name="blob-storage"></a>Blobb-lagring

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| BlobCapacity | Summan av Blob Storage som används i lagrings kontot. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 <br/> Dimensioner: **BlobType**och **BlobTier** ([definition](#metrics-dimensions)) |
| BlobCount    | Antalet BLOB-objekt som lagras i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 <br/> Dimensioner: **BlobType**och **BlobTier** ([definition](#metrics-dimensions)) |
| ContainerCount    | Antalet behållare i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| IndexCapacity     | Mängden lagrings utrymme som används av ADLS Gen2 hierarkiskt index <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

### <a name="table-storage"></a>Tabellagring

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| TableCapacity | Mängden tabell lagring som används av lagrings kontot. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| TableCount   | Antalet tabeller i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| TableEntityCount | Antalet tabell enheter i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| QueueCapacity | Mängden Queue Storage som används av lagrings kontot. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| QueueCount   | Antalet köer i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| QueueMessageCount | Antalet meddelanden som inte har förfallit i lagrings kontot. <br/><br/>Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

### <a name="file-storage"></a>Fillagring

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| FileCapacity | Mängden fil lagring som används av lagrings kontot. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| FileCount   | Antalet filer i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| FileShareCount | Antalet fil resurser i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

## <a name="transaction-metrics"></a>Transaktionsmått

Transaktions mått genereras på varje begäran till ett lagrings konto från Azure Storage till Azure Monitor. Om det inte finns någon aktivitet på ditt lagrings konto kommer det inte att finnas några data om transaktions måtten under perioden. Alla transaktions mått är tillgängliga både på konto-och service nivå (Blob Storage, Table Storage, Azure Files och Queue Storage). Tids kornig het definierar tidsintervallet som Mät värden presenteras. Tids korn som stöds för alla transaktions mått är PT1H och PT1M.

Azure Storage tillhandahåller följande transaktions mått i Azure Monitor.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Transaktioner | Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Tillämpliga dimensioner: ResponseType, typ av typ, ApiName och autentisering ([definition](#metrics-dimensions))<br/> Värde exempel: 1024 |
| Ingångshändelser | Mängden inkommande data. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. <br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| Utgående | Mängden utgående data. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. <br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| SuccessServerLatency | Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency. <br/><br/> Enhet: millisekunder <br/> Sammansättnings typ: genomsnitt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| SuccessE2ELatency | Den genomsnittliga svarstiden från slutpunkt till slutpunkt för lyckade begäranden som gjorts till en lagringstjänst eller för en angiven API-åtgärd. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret. <br/><br/> Enhet: millisekunder <br/> Sammansättnings typ: genomsnitt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| Tillgänglighet | Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även förfrågningar som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. <br/><br/> Enhet: procent <br/> Sammansättnings typ: genomsnitt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 99,99 |

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Storage stöder följande dimensioner för mått i Azure Monitor.

| Dimensions namn | Beskrivning |
| ------------------- | ----------------- |
| **BlobType** | Typ av BLOB för BLOB-mått. De värden som stöds är **BlockBlob**, **PageBlob**och **Azure Data Lake Storage**. Lägg till BLOB ingår i BlockBlob. |
| **BlobTier** | Azure Storage erbjuder olika åtkomst nivåer, vilket gör att du kan lagra BLOB-Datadata på det mest kostnads effektiva sättet. Läs mer i [Azure Storage BLOB-nivå](../blobs/storage-blob-storage-tiers.md). De värden som stöds är: <br/> <li>Frekvent **: frekvent**nivå</li> <li>Låg **frekvent: låg**frekvent nivå</li> <li>**Arkiv**: Arkiv lag ring</li> <li>**Premium**: Premium-nivån för Block-Blob</li> <li>**P4/P6/P10/p15/P20/P30/P40/P50/P60**: nivå typer för Premium Page-BLOB</li> <li>**Standard**: nivå typ för standard sid-BLOB</li> <li>Inte i **nivå**: nivå typ för generell användning v1 lagrings konto</li> |
| **Typ av typ** | Transaktion från primärt eller sekundärt kluster. De tillgängliga värdena är **primär** och **sekundär**. Den gäller för läsning av Geo-redundant lagring med Läs behörighet (RA-GRS) vid läsning av objekt från en sekundär klient. |
| **ResponseType** | Typ av transaktions svar. Tillgängliga värden är: <br/><br/> <li>**ServerOtherError**: alla andra fel på Server sidan förutom de som beskrivs </li> <li>**ServerBusyError**: autentiserad begäran som returnerade en HTTP 503-status kod. </li> <li>**ServerTimeoutError**: en timeout-autentiserad begäran som returnerade en status kod för HTTP 500. Tidsgränsen överskreds på grund av ett serverfel. </li> <li>**AuthorizationError**: autentiserad begäran som misslyckades på grund av obehörig åtkomst till data eller ett auktoriseringsfel. </li> <li>**NetworkError**: autentiserad begäran som misslyckades på grund av nätverks fel. Inträffar vanligen när klienten stänger en anslutning för tidigt innan tidsgränsen. </li> <li>**ClientThrottlingError**: begränsnings fel på klient sidan. </li> <li>**ClientTimeoutError**: en timeout-autentiserad begäran som returnerade en status kod för HTTP 500. Om klientens tidsgränser för nätverket eller förfrågningar är inställda på lägre värden än vad lagringstjänsten förväntar sig är det en förväntad timeout. Annars rapporteras den som ett ServerTimeoutError. </li> <li>**ClientOtherError**: alla andra fel på klient sidan, förutom de som beskrivs. </li> <li>**Lyckades**: lyckad begäran</li> <li> **SuccessWithThrottling**: lyckad begäran när en SMB-klient får en begränsning i de första försöken (erna) men lyckas efter återförsök.</li> |
| **ApiName** | Åtgärdens namn. Ett exempel: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> För alla åtgärds namn, se [dokument](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Autentisering** | Autentiseringstyp som används i transaktioner. Tillgängliga värden är: <br/> <li>**AccountKey**: transaktionen autentiseras med lagrings konto nyckeln.</li> <li>**SAS**: transaktionen autentiseras med signaturer för delad åtkomst.</li> <li>**OAuth**: transaktionen autentiseras med OAuth-åtkomsttoken.</li> <li>**Anonym**: transaktionen begärs anonymt. Den omfattar inte preflight-begäranden.</li> <li>**AnonymousPreflight**: transaktionen är en preflight-begäran.</li> |

För mått som stöder dimensioner måste du ange dimension svärdet för att se motsvarande mått värden. Om du till exempel tittar på **transaktions** värde för lyckade svar måste du filtrera **ResponseType** -dimensionen med **lyckat resultat**. Eller om du tittar på **BlobCount** -värdet för Block-Blob måste du filtrera **BlobType** -dimensionen med **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Tjänste kontinuitet för äldre mått

Äldre mått är tillgängliga parallellt med Azure Monitor hanterade mått. Supporten fortsätter på samma sätt tills Azure Storage avslutar tjänsten på äldre mått.

## <a name="faq"></a>FAQ

**Stöder nya mått det klassiska lagrings kontot?**

Nej, nya mått i Azure Monitor endast stöd för Azure Resource Manager lagrings konton. Om du vill använda mått på lagrings konton måste du migrera till Azure Resource Manager lagrings konto. Se [migrera till Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Stöder Azure Storage mått för Managed Disks eller ohanterade diskar?**

Nej, Azure Compute stöder mått på diskar. Mer information finns i [artikeln](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) .

**Hur mappar och migrerar du klassiska mått med nya mått?**

Du hittar detaljerad mappning mellan klassiska mått och nya mått i [Azure Storage metriska migreringar](./storage-metrics-migration.md).

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
