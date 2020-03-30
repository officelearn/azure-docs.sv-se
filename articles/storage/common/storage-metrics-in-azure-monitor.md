---
title: Azure Storage-mått i Azure Monitor | Microsoft-dokument
description: Läs mer om de nya måtten som erbjuds från Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 73181222bf3f15bbbac24fc253eddfea1c57bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247103"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure Storage-mått i Azure Monitor

Med mått på Azure Storage kan du analysera användningstrender, spåra begäranden och diagnostisera problem med ditt lagringskonto.

Azure Monitor tillhandahåller enhetliga användargränssnitt för övervakning av olika Azure-tjänster. Mer information finns i [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Azure Storage integrerar Azure Monitor genom att skicka måttdata till Azure Monitor-plattformen.

## <a name="access-metrics"></a>Åtkomstmått

Azure Monitor innehåller flera sätt att komma åt mått. Du kan komma åt dem från [Azure-portalen](https://portal.azure.com), Azure Monitor API:er (REST och .NET) och analyslösningar som Event Hubs. Mer information finns i [Azure Monitor Metrics](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Mått är aktiverade som standard och du kan komma åt de senaste 93 dagarnas data. Om du behöver behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta är konfigurerat i [diagnostikinställningar](../../azure-monitor/platform/platform-logs-overview.md) i Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Få tillgång till mått i Azure-portalen

Du kan övervaka mått över tid i Azure-portalen. I följande exempel visas hur du visar **transaktioner** på kontonivå.

![skärmbild av åtkomst till mått i Azure-portalen](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

För mått som stöder dimensioner kan du filtrera mått med önskat dimensionsvärde. I följande exempel visas hur du visar **transaktioner** på kontonivå på en viss åtgärd genom att välja värden för **API-namndimension.**

![skärmbild av åtkomst till mått med dimension i Azure-portalen](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Komma åt mått med REST API

Azure Monitor tillhandahåller [REST-API:er](/rest/api/monitor/) för att läsa måttdefinition och värden. I det här avsnittet visas hur du läser lagringsmåtten. Resurs-ID används i alla REST APIS. Mer information finns i Förstå resurs-ID för tjänster i lagring.

I följande exempel visas hur du använder ArmClient på kommandoraden för att förenkla testningen med REST API.The following example shows how to use [ArmClient](https://github.com/projectkudu/ARMClient) at the command line to simplify testing with the REST API.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Lista måttdefinition på kontonivå med REST API

I följande exempel visas hur du listar måttdefinition på kontonivå:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Om du vill visa måttdefinitioner för blob, tabell, fil eller kö måste du ange olika resurs-ID:er för varje tjänst med API:et.

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Läs måttvärden på kontonivå med REST API

I följande exempel visas hur du läser måttdata på kontonivå:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

I exemplet ovan, om du vill läsa måttvärden för blob, tabell, fil eller kö, måste du ange olika resurs-ID:er för varje tjänst med API:et.

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

### <a name="access-metrics-with-the-net-sdk"></a>Åtkomstmått med .NET SDK

Azure Monitor tillhandahåller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) för att läsa måttdefinition och värden. [Exempelkoden](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) visar hur du använder SDK med olika parametrar. Du måste `0.18.0-preview` använda eller senare version för lagringsmått. Resurs-ID används i .NET SDK. Mer information finns i Förstå resurs-ID för tjänster i lagring.

I följande exempel visas hur du använder Azure Monitor .NET SDK för att läsa lagringsmått.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Lista kontonivåmåttdefinition med .NET SDK

I följande exempel visas hur du listar måttdefinition på kontonivå:

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

Om du vill visa måttdefinitioner för blob, tabell, fil eller kö måste du ange olika resurs-ID:er för varje tjänst med API:et.

#### <a name="read-metric-values-with-the-net-sdk"></a>Läsa måttvärden med .NET SDK

I följande exempel visas `UsedCapacity` hur du läser data på kontonivå:

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

I exemplet ovan, om du vill läsa måttvärden för blob, tabell, fil eller kö, måste du ange olika resurs-ID:er för varje tjänst med API:et.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Läsa flerdimensionella måttvärden med .NET SDK

För flerdimensionella mått måste du definiera metadatafilter om du vill läsa måttdata på specifika dimensionsvärde.

I följande exempel visas hur du läser måttdata för måttet med stöd för flera dimensioner:

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

Resurs-ID är en unik identifierare för en resurs i Azure. När du använder AZURE Monitor REST API för att läsa måttdefinitioner eller värden måste du använda resurs-ID för den resurs som du tänker arbeta på. Resurs-ID-mallen följer det här formatet:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Lagring ger mått på både lagringskontonivå och tjänstnivå med Azure Monitor. Du kan till exempel hämta mått för bara Blob-lagring. Varje nivå har sitt eget resurs-ID, som används för att hämta måtten för just den nivån.

### <a name="resource-id-for-a-storage-account"></a>Resurs-ID för ett lagringskonto

Följande visar formatet för att ange resurs-ID för ett lagringskonto.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>Resurs-ID för lagringstjänsterna

Följande visar formatet för att ange resurs-ID för var och en av lagringstjänsterna.

* Resurs-ID för Blob-tjänst
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Resurs-ID för tabelltjänst
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Resurs-ID för kötjänst
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Resurs-ID för filtjänst
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Resurs-ID i REST-API FÖR Azure Monitor

Följande visar det mönster som används när du anropar AZURE Monitor REST API.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Kapacitetsmått
Kapacitetsmåttvärden skickas till Azure Monitor varje timme. Värdena uppdateras dagligen. Tidskornet definierar det tidsintervall för vilket måttvärden presenteras. Tidskornet som stöds för alla kapacitetsmått är en timme (PT1H).

Azure Storage tillhandahåller följande kapacitetsmått i Azure Monitor.

### <a name="account-level"></a>Kontonivå

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| UsedCapacity | Mängden lagringsutrymme som används av lagringskontot. För standardlagringskonton är det summan av den kapacitet som används av blobar, filer och köer. För Premium-lagringskonton och Blob Storage-konton är det samma sak som BlobCapacity. <br/><br/> Enhet: Byte <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| BlobCapacity | Summan av Blob-lagring som används i lagringskontot. <br/><br/> Enhet: Byte <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 <br/> Mått: **BlobType**och **BlobTier** ([Definition](#metrics-dimensions)) |
| BlobCount (BlobCount)    | Antalet blob-objekt som lagras i lagringskontot. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 <br/> Mått: **BlobType**och **BlobTier** ([Definition](#metrics-dimensions)) |
| ContainerCount (ContainerCount)    | Antalet behållare i lagringskontot. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |
| IndexKapacitet     | Mängden lagringsutrymme som används av ADLS Gen2 Hierarchical Index <br/><br/> Enhet: Byte <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |

### <a name="table-storage"></a>Table Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| TabellKapacitet | Mängden tabelllagring som används av lagringskontot. <br/><br/> Enhet: Byte <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |
| TabellCount   | Antalet tabeller i lagringskontot. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |
| TableEntityCount | Antalet tabellentiteter i lagringskontot. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Kökapacitet | Mängden kölagring som används av lagringskontot. <br/><br/> Enhet: Byte <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |
| QueueCount (QueueCount)   | Antalet köer i lagringskontot. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |
| QueueMessageCount | Antalet oexpirerade kömeddelanden i lagringskontot. <br/><br/>Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |

### <a name="file-storage"></a>File Storage

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Filkapacitet | Mängden fillagring som används av lagringskontot. <br/><br/> Enhet: Byte <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |
| FilCount   | Antalet filer i lagringskontot. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |
| Filsharecount | Antalet filresurser i lagringskontot. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/> Exempel på värde: 1024 |

## <a name="transaction-metrics"></a>Transaktionsmått

Transaktionsmått skickas på varje begäran till ett lagringskonto från Azure Storage till Azure Monitor. Om det inte finns någon aktivitet på ditt lagringskonto kommer det inte att finnas några data om transaktionsmått under perioden. Alla transaktionsmått är tillgängliga på både konto- och tjänstnivå (Blob-lagring, Tabelllagring, Azure-filer och kölagring). Tidskornet definierar tidsintervallet som måttvärden presenteras. Tidskornen som stöds för alla transaktionsmått är PT1H och PT1M.

Azure Storage innehåller följande transaktionsmått i Azure Monitor.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Transaktioner | Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Tillämpliga dimensioner: ResponseType, GeoType, ApiName och Autentisering ([Definition](#metrics-dimensions))<br/> Exempel på värde: 1024 |
| Ingress | Mängden inkommande data. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. <br/><br/> Enhet: Byte <br/> Aggregeringstyp: Totalt <br/> Tillämpliga dimensioner: GeoType, ApiName och Autentisering ([Definition](#metrics-dimensions)) <br/> Exempel på värde: 1024 |
| Utgående | Mängden utgående data. Det här numret inkluderar utgående till en extern klient från Azure Storage samt utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. <br/><br/> Enhet: Byte <br/> Aggregeringstyp: Totalt <br/> Tillämpliga dimensioner: GeoType, ApiName och Autentisering ([Definition](#metrics-dimensions)) <br/> Exempel på värde: 1024 |
| Lycka Till Svar | Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency. <br/><br/> Enhet: Millisekunder <br/> Aggregeringstyp: Medelvärde <br/> Tillämpliga dimensioner: GeoType, ApiName och Autentisering ([Definition](#metrics-dimensions)) <br/> Exempel på värde: 1024 |
| FramgångE2ELatency | Den genomsnittliga svarstiden från slutpunkt till slutpunkt för lyckade begäranden som gjorts till en lagringstjänst eller för en angiven API-åtgärd. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret. <br/><br/> Enhet: Millisekunder <br/> Aggregeringstyp: Medelvärde <br/> Tillämpliga dimensioner: GeoType, ApiName och Autentisering ([Definition](#metrics-dimensions)) <br/> Exempel på värde: 1024 |
| Tillgänglighet | Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även förfrågningar som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. <br/><br/> Enhet: Procent <br/> Aggregeringstyp: Medelvärde <br/> Tillämpliga dimensioner: GeoType, ApiName och Autentisering ([Definition](#metrics-dimensions)) <br/> Exempel på värde: 99,99 |

## <a name="metrics-dimensions"></a>Måttdimensioner

Azure Storage stöder följande dimensioner för mått i Azure Monitor.

| Dimensionsnamn | Beskrivning |
| ------------------- | ----------------- |
| **BlobType (BlobType)** | Endast typ av blob för Blob-mått. Värdena som stöds är **BlockBlob,** **PageBlob**och **Azure Data Lake Storage**. Append Blob ingår i BlockBlob. |
| **BlobTier (BlobTier)** | Azure storage erbjuder olika åtkomstnivåer, vilket gör att du kan lagra blob-objektdata på det mest kostnadseffektiva sättet. Visa mer i [Azure Storage blob-nivå](../blobs/storage-blob-storage-tiers.md). De värden som stöds inkluderar: <br/> <li>**Hot:** Hot nivå</li> <li>**Cool:** Cool nivå</li> <li>**Arkiv**: Arkivnivå</li> <li>**Premium**: Premium-nivå för blockblob</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Nivåtyper för premium sidblobb</li> <li>**Standard**: Nivåtyp för standardsida Blob</li> <li>**Ej tilltredd**: Nivåtyp för allmänt v1-lagringskonto</li> |
| **GeoType (olika)** | Transaktion från primärt eller sekundärt kluster. De tillgängliga värdena inkluderar **Primär** och **Sekundär**. Det gäller för Read Access Geo Redundant Storage (RA-GRS) när objekt läses från sekundär klient. |
| **Svarstyp** | Transaktionssvarstyp. Tillgängliga värden är: <br/><br/> <li>**ServerOtherError**: Alla andra fel på serversidan utom beskrivna </li> <li>**ServerBusyError**: Autentiserade begäran som returnerade en HTTP 503-statuskod. </li> <li>**ServerTimeoutError**: Timeout autentiserade begäran som returnerade en HTTP 500-statuskod. Tidsgränsen överskreds på grund av ett serverfel. </li> <li>**AuthorizationError**: Autentiserade begäran som misslyckades på grund av obehörig åtkomst av data eller ett auktoriseringsfel. </li> <li>**NetworkError**: Autentiserade begäran som misslyckades på grund av nätverksfel. Inträffar vanligen när klienten stänger en anslutning för tidigt innan tidsgränsen. </li>  <li>**ClientAccountBandwidthThrottlingError**: Begäran begränsas på bandbredd för att överskrida begränsningar för [lagringsansvarskalbarhet](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: Begäran begränsas på begäran för att överskrida begränsningar för [lagringsansvarskalbarhet](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: Andra begränsningsfel på klientsidan. ClientAccountBandwidthThrottlingError och ClientAccountRequestThrottlingError är exkluderade.</li> <li>**ClientTimeoutError**: Timeout autentiserade begäran som returnerade en HTTP 500-statuskod. Om klientens tidsgränser för nätverket eller förfrågningar är inställda på lägre värden än vad lagringstjänsten förväntar sig är det en förväntad timeout. Annars rapporteras den som ett ServerTimeoutError.</li> </li> <li>**ClientOtherError**: Alla andra fel på klientsidan utom beskrivna. </li> <li>**Framgång**: Lyckad begäran</li> <li> **SuccessWithThrottling**: Lyckad begäran när en SMB-klient begränsas i de första försöken men lyckas efter återförsök.</li> |
| **ApiName (ApiName)** | Namnet på åtgärden. Ett exempel: <br/> <li>**Skapacontainer**</li> <li>**DeleteBlob (ta bort)**</li> <li>**GetBlob (på andra et)**</li> För alla operationsnamn finns i [dokument](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Autentisering** | Autentiseringstyp som används i transaktioner. Tillgängliga värden är: <br/> <li>**AccountKey**: Transaktionen autentiseras med lagringskontonyckel.</li> <li>**SAS**: Transaktionen autentiseras med signaturer för delad åtkomst.</li> <li>**OAuth**: Transaktionen autentiseras med OAuth-åtkomsttoken.</li> <li>**Anonym**: Transaktionen begärs anonymt. Det inkluderar inte preflight förfrågningar.</li> <li>**AnonymousPreflight**: Transaktionen är preflight begäran.</li> |

För måtten som stöder dimensioner måste du ange dimensionsvärdet för att se motsvarande måttvärden. Om du till exempel tittar på **transaktionsvärde** för lyckade svar måste du filtrera **ResponseType-dimensionen** med **Framgång**. Eller om du tittar på **BlobCount-värdet** för Block Blob måste du filtrera **BlobType-dimensionen** med **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Tjänstekontinuitet för äldre mått

Äldre mått är tillgängliga parallellt med Azure Monitor-hanterade mått. Supporten håller samma tills Azure Storage avslutar tjänsten på äldre mått.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Stöder nya mått Classic Storage-konto?**

Nej, nya mått i Azure Monitor stöder endast Azure Resource Manager-lagringskonton. Om du vill använda mått på lagringskonton måste du migrera till Azure Resource Manager Storage-konto. Se [Migrera till Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Stöder Azure Storage mått för hanterade diskar eller ohanterade diskar?**

Nej, Azure Compute stöder måtten på diskar. Se [artikel](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) för mer information.

**Hur man kartlägger och migrerar klassiska mätvärden med nya mått?**

Du kan hitta detaljerad mappning mellan klassiska mått och nya mått i [Azure Storage-måttmigrering](./storage-metrics-migration.md).

## <a name="next-steps"></a>Nästa steg

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
