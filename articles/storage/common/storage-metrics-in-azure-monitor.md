---
title: Azure Storage-mätvärden i Azure-Monitor | Microsoft Docs
description: Mer information om nya mått som erbjuds från Azure-Monitor.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/05/2017
ms.author: fryu
ms.openlocfilehash: b1d82f9b527a62109e0301907b87bd683f9912af
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure Storage-mått i Azure Monitor

Du kan analysera användningstrender, spåra förfrågningar och diagnostisera problem med ditt lagringskonto med mått på Azure Storage.

Azure-Monitor ger enhetlig användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [Azure-Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Azure Storage integreras Azure-Monitor genom att skicka mätvärden för Azure-Monitor-plattformen.

## <a name="access-metrics"></a>Åtkomst-mått

Azure-Monitor finns flera sätt att åtkomst mått. Du kan komma åt dem från den [Azure-portalen](https://portal.azure.com), Azure övervakaren API: er (REST och .net) och lösningar för analys, till exempel åtgärden Management Suite och Händelsehubbar. Mer information finns i [mått för Azure-Monitor](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna av data. Du kan arkivera mått data till ett Azure Storage-konto om du vill behålla data under en längre tidsperiod. Detta är konfigurerat i [diagnostikinställningar](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) i Azure-Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Åtkomst till mätvärden i Azure-portalen

Du kan övervaka mått med tiden i Azure-portalen. I följande exempel visas hur du visar **UsedCapacity** på kontonivå.

![Skärmbild av åtkomst till mätvärden i Azure-portalen](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

För statistik som stöd för dimensioner, måste du filtrera med värdet för önskade dimensionen. I följande exempel visas hur du visar **transaktioner** på kontonivå med **lyckade** svarstyp.

![Skärmbild av åtkomst till mått med dimension i Azure-portalen](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Åtkomst mått med REST API

Azure övervakning ger [REST API: er](/rest/api/monitor/) att läsa mått definition och värden. Det här avsnittet visar hur du läsa måtten för lagring. Resurs-ID används i alla REST API: er. Mer information finns [förstå resurs-ID för tjänster i lagring](#understanding-resource-id-for-services-in-storage).

I följande exempel visas hur du använder [ArmClient](https://github.com/projectkudu/ARMClient) på kommandoraden för att förenkla testning med REST API.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Listan konto nivån mått definition med REST API

I följande exempel visas hur du listar mått definition på nivån för kontot:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

Om du vill visa en lista med mått definitioner för blob-, tabell-, fil- eller kön måste du ange olika resurs-ID för varje tjänst med API.

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Läsa kontonivå måttvärden med REST API

I följande exempel visas hur de ska läsa mått data på nivån för kontot:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

I ovanstående exempel måste om du vill läsa måttvärden för blob, tabell-, fil- eller kö, du ange olika resurs-ID för varje tjänst med API.

Följande svaret innehåller måttvärden i JSON-format:

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

### <a name="access-metrics-with-the-net-sdk"></a>Åtkomst mått med .net SDK

Azure övervakning ger [.Net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) att läsa mått definition och värden. Den [exempelkoden](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) visar hur du använder SDK: N med andra parametrar. Du måste använda `0.18.0-preview` eller senare version för storage-mätvärden. Resurs-ID används i .net SDK. Mer information finns [förstå resurs-ID för tjänster i lagring](#understanding-resource-id-for-services-in-storage).

I följande exempel visas hur du använder Azure övervaka .net SDK för att läsa måtten för lagring.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Listan konto nivån mått definition med .net SDK

I följande exempel visas hur du listar mått definition på nivån för kontot:

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

Om du vill visa en lista med mått definitioner för blob-, tabell-, fil- eller kön måste du ange olika resurs-ID för varje tjänst med API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Läs måttvärden med .net SDK

I följande exempel visas hur du läser `UsedCapacity` data på nivån för kontot:

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

I ovanstående exempel måste om du vill läsa måttvärden för blob, tabell-, fil- eller kö, du ange olika resurs-ID för varje tjänst med API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Läsa flerdimensionella måttvärden med .net SDK

För flerdimensionella statistik måste du definiera filter för meta-data om du vill läsa mått data på en specifik dimensionsvärde.

I följande exempel visas hur du läser mått data på det mått som stöder flera dimension:

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

Resurs-ID är ett unikt ID för en resurs i Azure. När du använder Azure övervakaren REST API läsa mått definitioner eller värden, använder du resurs-ID för den resurs som du tänker använda. Resurs-ID mallen följer det här formatet:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

Lagring ger mått på både lagringsnivå för kontot och servicenivå med Azure-Monitor. Du kan till exempel hämta mätvärden för bara Blob storage. Varje nivå har en egen resurs-ID som används för att hämta mätvärden för just den nivån.

### <a name="resource-id-for-a-storage-account"></a>Resurs-ID för ett lagringskonto

Nedan visas ett format för att ange resurs-ID för ett lagringskonto.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>Resurs-ID för storage-tjänster

Nedan visas ett format för att ange resurs-ID för var och en av lagringstjänsterna.

* Resurs-ID för BLOB-tjänst `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* Tabellen service resurs-ID `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* Kön service resurs-ID `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* Filen service resurs-ID `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>Resurs-ID i Azure-Monitor REST-API

Nedan visas ett mönster som ska användas när Azure övervakaren REST API.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Kapacitet mått
Kapacitet mått-värden skickas till Azure-Monitor varje timme. Värdena uppdateras dagligen. Tidskornet definierar det tidsintervall som mått värden anges. Stöds tidsenhet för alla kapacitetsdata är en timme (PT1H).

Azure Storage tillhandahåller följande kapacitetsdata i Azure-Monitor.

### <a name="account-level"></a>Kontonivå

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| UsedCapacity | Mängden lagringsutrymme som används av storage-konto. Standard storage-konton är det summan av kapacitet som används av blob-, tabell-, fil- och kön. Det är samma som BlobCapacity för premium-lagringskonton och Blob storage-konton. <br/><br/> Enhet: byte <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| BlobCapacity | Summan av Blob-lagring som används i lagringskontot. <br/><br/> Enhet: byte <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 <br/> Dimensionen: BlobType ([Definition](#metrics-dimensions)) |
| BlobCount    | Antal blob-objekt som lagras i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 <br/> Dimensionen: BlobType ([Definition](#metrics-dimensions)) |
| ContainerCount    | Antal behållare i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |

### <a name="table-storage"></a>Table Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| TableCapacity | Mängden tabellagring som används av storage-konto. <br/><br/> Enhet: byte <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| TableCount   | Antalet tabeller i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| TableEntityCount | Antal tabellentiteter i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| QueueCapacity | Mängden Queue storage som används av storage-konto. <br/><br/> Enhet: byte <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| QueueCount   | Antalet köer i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| QueueMessageCount | Antalet återstående Kömeddelanden i storage-konto. <br/><br/>Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |

### <a name="file-storage"></a>File Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| FileCapacity | Mängden fillagring som används av storage-konto. <br/><br/> Enhet: byte <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| FileCount   | Antal filer i lagringskontot. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |
| FileShareCount | Antal delar i storage-konto. <br/><br/> Enhet: antal <br/> Sammansättningstyp: genomsnittlig <br/> Värdeexempel: 1024 |

## <a name="transaction-metrics"></a>Transaktionen mått

Transaktionen mått skickas från Azure Storage till Azure-Monitor varje minut. Alla mätvärden för transaktion är tillgängliga på både konto och servicenivå (Blob storage, Table storage, Azure-filer och Queue storage). Tidskornet definierar det tidsintervall som måttvärden visas. Tid som stöds kärnor för alla transaktioner mått är PT1H och PT1M.

Azure Storage tillhandahåller följande transaktion mått i Azure-Monitor.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Transaktioner | Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Tillämpliga dimensioner: ResponseType, GeoType, ApiName ([Definition](#metrics-dimensions))<br/> Värdeexempel: 1024 |
| Ingångshändelser | Mängden inkommande data. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. <br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt <br/> Tillämpliga dimensioner: GeoType, ApiName ([Definition](#metrics-dimensions)) <br/> Värdeexempel: 1024 |
| Egress | Mängden utgående data. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. <br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt <br/> Tillämpliga dimensioner: GeoType, ApiName ([Definition](#metrics-dimensions)) <br/> Värdeexempel: 1024 |
| SuccessServerLatency | Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency. <br/><br/> Enhet: millisekunder <br/> Sammansättningstyp: genomsnittlig <br/> Tillämpliga dimensioner: GeoType, ApiName ([Definition](#metrics-dimensions)) <br/> Värdeexempel: 1024 |
| SuccessE2ELatency | Den genomsnittliga svarstiden från slutpunkt till slutpunkt för lyckade begäranden som gjorts till en lagringstjänst eller för en angiven API-åtgärd. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret. <br/><br/> Enhet: millisekunder <br/> Sammansättningstyp: genomsnittlig <br/> Tillämpliga dimensioner: GeoType, ApiName ([Definition](#metrics-dimensions)) <br/> Värdeexempel: 1024 |
| Tillgänglighet | Procentandelen av tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgänglighet beräknas genom att använda värdet fakturerbar förfrågningarna och dividera med antalet tillämpliga begäranden, inklusive de förfrågningar som producerade oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. <br/><br/> Enhet: procent <br/> Sammansättningstyp: genomsnittlig <br/> Tillämpliga dimensioner: GeoType, ApiName ([Definition](#metrics-dimensions)) <br/> Värdeexempel: 99,99 |

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Storage har stöd för följande dimensioner för mätvärden i Azure-Monitor.

| Dimensionsnamn | Beskrivning |
| ------------------- | ----------------- |
| BlobType | Typ av blob för endast Blob-mått. Värdena som stöds är **BlockBlob** och **PageBlob**. Lägg till Blob ingår i BlockBlob. |
| ResponseType | Transaktionstyp för svar. Tillgängliga värden är: <br/><br/> <li>ServerOtherError: Alla serversidan fel förutom beskrivs de </li> <li> ServerBusyError: Begäran som returnerade ett HTTP 503-statuskoden. </li> <li> ServerTimeoutError: Timeout begäran som returnerade en HTTP 500-statuskod. Timeout uppstod på grund av ett serverfel. </li> <li> AuthorizationError: Begäran som misslyckades på grund av obehörig åtkomst av data eller ett auktoriseringsfel. </li> <li> NetworkError: Begäran som misslyckades på grund av nätverksfel. Inträffar oftast när en klient stänger en anslutning innan tidsgränsen upphör att gälla för tidigt. </li> <li>    ClientThrottlingError: Klientsidan bandbreddsbegränsning fel. </li> <li> ClientTimeoutError: Timeout begäran som returnerade en HTTP 500-statuskod. Om klientens nätverk eller timeout för begäranden har angetts till ett lägre värde än förväntat genom att lagringstjänsten är en förväntad timeout. Annars rapporteras den som en ServerTimeoutError. </li> <li> ClientOtherError: Alla klientsidan fel utom beskrivs de. </li> <li> Lyckades: Lyckade begäranden|
| GeoType | Transaktion från primär eller sekundär kluster. Tillgängliga värden är primär och sekundär. Det gäller läsbehörighet Geo-Redundant Storage(RA-GRS) vid läsning av objekt från sekundär klient. |
| apiName | Namnet på åtgärden. Exempel: <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Namn på åtgärden, se [dokument](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md). |

För mått stödjande dimensioner måste du ange dimensionsvärdet om du vill se motsvarande mått värden. Om du tittar på exempelvis **transaktioner** värdet för lyckad svar du vill filtrera den **ResponseType** med **lyckade**. Eller om du tittar på **BlobCount** värdet för Blockblob, måste du filtrera den **BlobType** med **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Kontinuitet för äldre mått

Äldre är tillgängligt parallellt med Azure-Monitor hanteras. Stöd för att hålla samma tills Azure Storage avslutar tjänsten på äldre mått.

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
