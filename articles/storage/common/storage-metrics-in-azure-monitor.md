---
title: Azure Storage-mått i Azure Monitor | Microsoft Docs
description: Läs mer om den nya mått som erbjuds från Azure Monitor.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 09/05/2017
ms.author: fryu
ms.component: common
ms.openlocfilehash: 849253dd4a2e66acc6a509a0515a22309c90e081
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42061464"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure Storage-mått i Azure Monitor

Med mått i Azure Storage kan du analysera användningstrender, spåra förfrågningar och diagnostisera problem med ditt lagringskonto.

Azure Monitor innehåller enhetligt användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Azure Storage integreras Azure Monitor genom att skicka måttdata till Azure Monitor-plattformen.

## <a name="access-metrics"></a>Åtkomst-mått

Azure Monitor innehåller flera sätt att åtkomst mått. Du kan komma åt dem från den [Azure-portalen](https://portal.azure.com), Azure Monitor-API: er (REST och .net) och lösningar för dataanalys, till exempel Operations Management Suite och Event Hubs. Mer information finns i [Azure Monitor Metrics](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna data. Om du vill behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta är konfigurerat i [diagnostikinställningar](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) i Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Åtkomst-mått i Azure portal

Du kan övervaka mått med tiden i Azure-portalen. I följande exempel visas hur du visar **UsedCapacity** på kontonivå.

![Skärmbild för att komma åt mått i Azure portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Du kan filtrera mått med önskad dimension-värde för mått som stöder dimensioner. I följande exempel visas hur du visar **transaktioner** på kontonivå på en viss åtgärd genom att välja värden för **API-namn** dimension.

![Skärmbild för att komma åt mått med dimensionen i Azure portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Mått för åtkomst med REST API

Azure Monitor innehåller [REST API: er](/rest/api/monitor/) att läsa måttdefinition och värden. Det här avsnittet visas hur du läser storage-mått. Resurs-ID används i alla REST API: er. Mer information hittar du [förstå resurs-ID för tjänster i Storage](#understanding-resource-id-for-services-in-storage).

I följande exempel visas hur du använder [ArmClient](https://github.com/projectkudu/ARMClient) på kommandoraden för att förenkla testa med REST API.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Lista konto på måttdefinition med REST API

I följande exempel visar hur du listar måttdefinition på kontonivå:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

Om du vill visa en lista över måttdefinitioner för blob-, tabell-, fil- eller kön, måste du ange olika resurs-ID för varje tjänst med API: et.

Svaret innehåller måttdefinitionen i JSON-format:

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Läsa kontonivå måttvärden med REST API

I följande exempel visas hur du läser måttdata på kontonivå:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

I ovan exempelvis måste om du vill läsa måttvärden för blob-, tabell-, fil- eller kö, du ange olika resurs-ID för varje tjänst med API: et.

Följande svar innehåller måttvärden i JSON-format:

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

### <a name="access-metrics-with-the-net-sdk"></a>Åtkomst till mått med .net SDK

Azure Monitor innehåller [.Net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) att läsa måttdefinition och värden. Den [exempelkoden](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) visar hur du använder SDK: N med olika parametrar. Du måste använda `0.18.0-preview` eller senare för storage-mått. Resurs-ID används i .net SDK. Mer information hittar du [förstå resurs-ID för tjänster i Storage](#understanding-resource-id-for-services-in-storage).

I följande exempel visar hur du läser mätvärden i storage med Azure Monitor .net SDK.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Lista konto på måttdefinition med .net SDK

I följande exempel visar hur du listar måttdefinition på kontonivå:

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
        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
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

Om du vill visa en lista över måttdefinitioner för blob-, tabell-, fil- eller kön, måste du ange olika resurs-ID för varje tjänst med API: et.

#### <a name="read-metric-values-with-the-net-sdk"></a>Läsa måttvärden med .net SDK

I följande exempel visas hur du läser `UsedCapacity` data på kontonivå:

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

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metric: "UsedCapacity",

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

I ovan exempelvis måste om du vill läsa måttvärden för blob-, tabell-, fil- eller kö, du ange olika resurs-ID för varje tjänst med API: et.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Läsa flerdimensionella måttvärden med .net SDK

För flerdimensionella mått som du behöver definiera filter för meta-data om du vill läsa måttdata på specifika dimensionsvärde.

I följande exempel visas hur du läser måttdata på mått som stöd för måttkommandon:

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

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metric: "BlobCapacity",
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

Resurs-ID är en unik identifierare för en resurs i Azure. När du använder Azure Monitor REST API för att läsa måttdefinitioner av eller värden, måste du använda resurs-ID för den resurs som du tänker använda. ID-resursmall följer det här formatet:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

Storage tillhandahåller mått på både lagringskontonivån och servicenivå med Azure Monitor. Du kan till exempel hämta mätvärden för bara Blob storage. Varje nivå har en egen resurs-ID som används för att hämta mått för just den nivån.

### <a name="resource-id-for-a-storage-account"></a>Resurs-ID för ett lagringskonto

Nedan visas format för att ange resurs-ID för ett lagringskonto.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>Resurs-ID för storage-tjänster

Nedan visas format för att ange resurs-ID för var och en av lagringstjänsterna.

* Resurs-ID för BLOB service `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* Resurs-ID för Table service `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* Resurs-ID för kö-tjänst `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* Filen service resurs-ID `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>Resurs-ID i Azure Monitor REST-API

Nedan visas det mönster som används när du anropar REST-API i Azure Monitor.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Kapacitet mått
Kapacitet mått-värden skickas till Azure Monitor varje timme. Värden uppdateras dagligen. Tidskornet definierar tidsintervallet som mått visas. Tidsintervallet som stöds för alla kapacitet är en timme (PT1H).

Azure Storage tillhandahåller följande kapacitet i Azure Monitor.

### <a name="account-level"></a>Kontonivå

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| UsedCapacity | Mängden lagringsutrymme som används av storage-konto. För standardlagringskonton är det summan av kapacitet som används av blob-, tabell-, fil- och kö. Det är samma som BlobCapacity för premium storage-konton och Blob storage-konton. <br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Värdeexempel: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| BlobCapacity | Summan av Blob-lagring som används i lagringskontot. <br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Värdeexempel: 1024 <br/> Dimensionen: BlobType ([Definition](#metrics-dimensions)) |
| BlobCount    | Antal blob-objekt som lagras i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Värdeexempel: 1024 <br/> Dimensionen: BlobType ([Definition](#metrics-dimensions)) |
| ContainerCount    | Antalet behållare i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |

### <a name="table-storage"></a>Table Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| TableCapacity | Mängden tabellagring som används av storage-konto. <br/><br/> Enhet: byte <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| TableCount   | Antalet tabeller i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| TableEntityCount | Antalet tabellentiteter i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| QueueCapacity | Mängden kölagring som används av storage-konto. <br/><br/> Enhet: byte <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| QueueCount   | Antalet köer i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| QueueMessageCount | Antal läggs Kömeddelanden i lagringskontot. <br/><br/>Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |

### <a name="file-storage"></a>File Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| FileCapacity | Mängden fillagring som används av storage-konto. <br/><br/> Enhet: byte <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| FileCount   | Antal filer i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| FileShareCount | Antal filresurser i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |

## <a name="transaction-metrics"></a>Transaktionsmått

Transaktionsmått skickas från Azure Storage till Azure Monitor varje minut. Alla transaktionsmått är tillgängliga på konto- och service (Blob storage, Table storage, Azure Files och Queue storage). Tidskornet definierar tidsintervallet som måttvärden visas. Stöds tid kärnor för alla transaktionsmått är PT1H och PT1M.

Azure Storage tillhandahåller följande transaktionsmått i Azure Monitor.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Transaktioner | Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Tillämpliga dimensioner: ResponseType, GeoType, ApiName och autentisering ([Definition](#metrics-dimensions))<br/> Värdeexempel: 1024 |
| Ingångshändelser | Mängden inkommande data. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. <br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Tillämpliga dimensioner: GeoType, ApiName och autentisering ([Definition](#metrics-dimensions)) <br/> Värdeexempel: 1024 |
| Egress | Mängden utgående data. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. <br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Tillämpliga dimensioner: GeoType, ApiName och autentisering ([Definition](#metrics-dimensions)) <br/> Värdeexempel: 1024 |
| SuccessServerLatency | Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency. <br/><br/> Enhet: millisekunder <br/> Sammansättningstyp: genomsnittlig <br/> Tillämpliga dimensioner: GeoType, ApiName och autentisering ([Definition](#metrics-dimensions)) <br/> Värdeexempel: 1024 |
| SuccessE2ELatency | Den genomsnittliga svarstiden från slutpunkt till slutpunkt för lyckade begäranden som gjorts till en lagringstjänst eller för en angiven API-åtgärd. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret. <br/><br/> Enhet: millisekunder <br/> Sammansättningstyp: genomsnittlig <br/> Tillämpliga dimensioner: GeoType, ApiName och autentisering ([Definition](#metrics-dimensions)) <br/> Värdeexempel: 1024 |
| Tillgänglighet | Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att använda värdet för totalt antal debiterbara begäranden och dividera det med antalet tillämpliga förfrågningar, inklusive de begäranden som genererat ett oväntat fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. <br/><br/> Enhet: procent <br/> Sammansättningstyp: genomsnittlig <br/> Tillämpliga dimensioner: GeoType, ApiName och autentisering ([Definition](#metrics-dimensions)) <br/> Värdeexempel: 99,99 |

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Storage stöder följande dimensioner för mått i Azure Monitor.

| Dimensionsnamn | Beskrivning |
| ------------------- | ----------------- |
| BlobType | Typen av blobb endast Blob-mått. Godkända värden är **BlockBlob** och **PageBlob**. Lägg till Blob som ingår i BlockBlob. |
| ResponseType | Transaktionstyp vid svar. Tillgängliga värden är: <br/><br/> <li>ServerOtherError: Alla serversidan fel förutom beskrivs de </li> <li> ServerBusyError: Autentiserad begäran som returnerade statuskoden HTTP 503-fel. </li> <li> ServerTimeoutError: Tidsgränsen autentiserad begäran som returnerade ett HTTP 500-statuskoden. Timeout uppstod på grund av ett serverfel. </li> <li> AuthorizationError: Autentiserad begäran som misslyckades på grund av obehörig åtkomst av data eller ett autentiseringsfel. </li> <li> NetworkError: Autentiserad begäran som misslyckades på grund av nätverksfel. Inträffar vanligen när en klient stängs för tidigt en anslutning innan tidsgränsen upphör att gälla. </li> <li>    ClientThrottlingError: Begränsning på klientsidan fel. </li> <li> ClientTimeoutError: Tidsgränsen autentiserad begäran som returnerade ett HTTP 500-statuskoden. Om klientens tidsgränsen uppnåddes för nätverket eller timeout för begäran har angetts till ett lägre värde än förväntat genom att lagringstjänsten är det en förväntad timeout. Annars rapporteras den som en ServerTimeoutError. </li> <li> ClientOtherError: Alla andra klientsidan fel utom beskrivs de. </li> <li> Lyckades: Lyckad begäran|
| GeoType | Transaktionen från primär eller sekundär kluster. Tillgängliga värden är primär och sekundär. Den gäller Read Access Geo Redundant Storage(RA-GRS) vid läsning av objekt från sekundär klient. |
| ApiName | Namnet på åtgärden. Exempel: <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Namn på åtgärden, se [dokumentet](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md). |
| Autentisering | Autentiseringstypen som används i transaktioner. Tillgängliga värden är: <br/> <li>AccountKey: Transaktionen har verifierats med din lagringskontonyckel.</li> <li>SAS: Transaktionen har verifierats med signaturer för delad åtkomst.</li> <li>OAuth: Transaktionen har verifierats med OAuth-åtkomsttoken.</li> <li>Anonym: Transaktionen har begärts anonymt. De omfattar inte preflight-begäranden.</li> <li>AnonymousPreflight: Transaktionen är preflight-begäran.</li> |

Du måste ange dimensionsvärde för att se de motsvarande mått för stödjande dimensioner mått. Exempel: Om du tittar på **transaktioner** värde för lyckade svar du behöver att filtrera de **ResponseType** med **lyckades**. Eller om du tittar på **BlobCount** värdet för Blockblob, måste du filtrera den **BlobType** med **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Kontinuitet äldre mått

Äldre mått är tillgängliga parallellt med Azure Monitor hanteras mått. Stödet behåller samma tills Azure Storage avslutar tjänsten på äldre mått.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Stöder Azure Storage mått för hanterade eller ohanterade diskar?**

Nej, Azure Compute har stöd för mått på diskar. Se [artikeln](https://azure.microsoft.com/en-us/blog/per-disk-metrics-managed-disks/) för mer information.

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
