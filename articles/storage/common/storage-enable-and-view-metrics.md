---
title: "Aktivera storage-mätvärden i Azure portal | Microsoft Docs"
description: "Så här aktiverar du storage-mätvärden för Blob, kön, tabell och filen tjänster"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: tamram
ms.openlocfilehash: 8abb4f968c1fa84e03c8cc807826d3684713847a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Aktivera Azure Storage-mätvärden och visa mått data
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Översikt
Storage-mätvärden är aktiverad som standard när du skapar ett nytt lagringskonto. Du kan konfigurera övervakning den [Azure-portalen](https://portal.azure.com) eller Windows PowerShell eller programmässigt via en lagringsklientbiblioteken.

Du kan konfigurera en kvarhållningsperiod för metrics data: den här perioden anger hur länge lagring tjänsten håller mått och avgifter som du för utrymmet krävs för att lagra dem. Du bör normalt använda en kortare period för minut mått än timvis mått på grund av betydande extra utrymme som krävs för minut mått. Välj en kvarhållningsperiod så att du har tillräckligt med tid att analysera data och hämta några mått som du vill behålla för offline-analys och rapportering. Kom ihåg att du debiteras också för att ladda ned mått data från ditt lagringskonto.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Så här aktiverar du mått med Azure-portalen
Följ dessa steg om du vill aktivera mätvärden i den [Azure-portalen](https://portal.azure.com):

1. Navigera till ditt lagringskonto.
1. Välj **diagnostik** från den **menyn** fönstret.
1. Se till att **Status** är inställd på **på**.
1. Välj mått för de tjänster som du vill övervaka.
1. Ange en bevarandeprincip som anger hur lång tid att behålla mått och logga data.
1. Välj **Spara**.

Den [Azure-portalen](https://portal.azure.com) för närvarande kan du inte konfigurera minut mått i ditt lagringskonto, måste du aktivera minut mått med PowerShell eller programmässigt.

## <a name="how-to-enable-metrics-using-powershell"></a>Så här aktiverar du mått med PowerShell
Du kan använda PowerShell på den lokala datorn för att konfigurera måtten för lagring i ditt lagringskonto med hjälp av Azure PowerShell-cmdleten Get-AzureStorageServiceMetricsProperty för att hämta de aktuella inställningarna och cmdlet Set-AzureStorageServiceMetricsProperty för att ändra de aktuella inställningarna.

Cmdletar som styr Storage-mätvärden använder följande parametrar:

* MetricsType: möjliga värden är timmar och minuter.
* ServiceType: möjliga värden är Blob, köer och tabellen.
* MetricsLevel: möjliga värden är None, tjänst och ServiceAndApi.

Till exempel växlar följande kommando på minut mätvärden för Blob-tjänsten i standardkontot för lagring med kvarhållning tidsperiod som angetts till fem dagar:

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

Följande kommando hämtar de aktuella timvis mått nivå och kvarhållning dagarna för blob-tjänsten i standardkontot för lagring:

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Information om hur du konfigurerar Azure PowerShell-cmdlets för att arbeta med din Azure-prenumeration och hur du väljer standardkontot för lagring ska användas, finns: [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Så här aktiverar du Storage-mätvärden via programmering
Följande C# utdrag visar hur du aktiverar loggning för Blob-tjänsten med storage-klientbiblioteket för .NET- och mått:

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Visa Storage-mätvärden
När du har konfigurerat Storage Analytics mätvärden för att övervaka ditt lagringskonto registrerar Storage Analytics mätvärden i en uppsättning välkända tabeller i ditt lagringskonto. Du kan konfigurera diagram om du vill visa timvis mått i den [Azure-portalen](https://portal.azure.com):

1. Navigera till ditt lagringskonto i den [Azure-portalen](https://portal.azure.com).
1. Välj **mått** i den **menyn** fönstret för tjänsten vars mått som du vill visa.
1. Välj **redigera** på diagrammet som du vill konfigurera.
1. I den **redigera diagram** väljer den **tidsintervall**, **diagramtypen**, och det mått som ska visas i diagrammet.
1. Välj **OK**

Om du vill hämta mätvärden för långsiktig lagring eller analysera dem lokalt, måste du:

* Använd ett verktyg som känner till dessa tabeller och gör att du kan visa och ladda ned dem.
* Skriv ett anpassat program eller skript för att läsa och lagra tabeller.

Surfa på storage-verktyg för många från tredje part är medveten om dessa tabeller och gör att du kan visa dem direkt.
Se [Azure Storage-klientverktyg](storage-explorers.md) en lista över tillgängliga verktyg.

> [!NOTE]
> Från och med version 0.8.0 av den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/), kan du visa och hämta analytics mätvärden tabeller.
> 
> 

För att komma åt tabellerna analytics programmässigt Observera analytics tabeller inte visas om du en lista över alla tabeller i ditt lagringskonto. Du kan antingen komma åt dem direkt efter namn eller använda den [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) i .NET-klientbibliotek för att fråga tabellnamnen.

### <a name="hourly-metrics"></a>Varje timme mått
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Minut mått
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Kapacitet
* $MetricsCapacityBlob

Du kan hitta fullständig information om scheman för dessa tabeller på [Storage Analytics mätvärden tabellschemat](https://msdn.microsoft.com/library/azure/hh343264.aspx). Raderna exemplet nedan visar endast en delmängd av kolumnerna som är tillgängliga, men illustrera vissa viktiga funktioner av Storage-mätvärden sparar dessa mått:

| PartitionKey | RowKey | tidsstämpel | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Tillgänglighet | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |användaren. Alla |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |användaren. QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |användaren. QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |användaren. UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

I det här exemplet minut mätvärdesdata kan används Partitionsnyckeln vid matchning av minut. Radnyckeln identifierar typ av information som lagras på raden. Radnyckeln består av två typer av information, vilken åtkomsttyp och typ av begäran:

* Åtkomsttypen är användaren eller systemet, där användaren avser alla användarförfrågningar till storage-tjänsten och system avser förfrågningar som görs av Storage Analytics.
* Typ av begäran är antingen i vilket fall det är en sammanfattande rad eller identifierar det specifika API: et, till exempel QueryEntity eller UpdateEntity.

Exempeldata ovan visar alla poster för en minut (börjar på 11:00:00), så att antalet begäranden som QueryEntities plus antalet QueryEntity begär plus antalet UpdateEntity manuellt lägga till upp till sju, vilket är det totala antalet visas på raden för användaren: All. Du kan dessutom härleda slutpunkt till slutpunkt genomsnittlig svarstid 104.4286 på raden för användaren: All genom en beräkning ((143.8 * 5) + 3 + 9) / 7.

## <a name="metrics-alerts"></a>Mått aviseringar
Du bör du överväga att konfigurera aviseringar i den [Azure-portalen](https://portal.azure.com) så Storage-mätvärden automatiskt meddela dig om viktiga ändringar i beteendet för storage-tjänster. Om du använder ett verktyg för lagring explorer för att hämta informationen mått i en avgränsad format kan du använda Microsoft Excel för att analysera data. Se [Azure Storage-klientverktyg](storage-explorers.md) för en lista över tillgängliga lagringsutrymmet explorer verktyg. Du kan konfigurera aviseringar i den **Varna regler** rutan tillgänglig under **övervakning** i fönstret Storage-konto-menyn.

> [!IMPORTANT]
> Det kan finnas en fördröjning mellan en händelse för lagring och när motsvarande timvis eller minut mätvärdesdata registreras. När du loggar minut mått kan flera minuter av data skrivas på samma gång. Transaktioner från tidigare minuter kan sedan samman i transaktionen för den aktuella minuten. När detta inträffar kanske alert-tjänsten inte alla tillgängliga mått data för konfigurerade avisering intervall, vilket kan leda till oväntat startar aviseringar.
>

## <a name="accessing-metrics-data-programmatically"></a>Dataåtkomst mätvärden via programmering
Följande lista visar exempel C#-kod som har åtkomst till minut mått för ett antal minuter och visar resultatet i ett konsolfönster. Den använder Azure Storage-biblioteket version 4 som innehåller klassen CloudAnalyticsClient som gör det enklare att komma åt mått tabellerna i lagring.

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
{
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
        Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
        var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
        var t = analyticsClient.GetMinuteMetricsTable(service);
        var opContext = new OperationContext();
        var query =
          from entity in metricsQuery
          // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
          // because they are calculated fields in the MetricsEntity class.
          // The PartitionKey identifies the DataTime of the metrics.
          where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0
        select entity;

        // Filter on "user" transactions after fetching the metrics from Table Storage.
        // (StartsWith is not supported using LINQ with Azure table storage)
        var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
        var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
        Console.WriteLine(resultString);
    }
}

private static string MetricsString(MetricsEntity entity, OperationContext opContext)
{
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
      string.Format("Time: {0}, ", entity.Time) +
      string.Format("AccessType: {0}, ", entity.AccessType) +
      string.Format("TransactionType: {0}, ", entity.TransactionType) +
      string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
}
```

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Vilka avgifter gör uppkommer när du aktiverar lagring mått?
Skriva begäranden om att skapa tabellentiteter för mått debiteras enligt standardpriser gäller för alla Azure Storage-åtgärder.

Förfrågningar om läsning och borttagning av en klient till mätvärdesdata är också fakturerbar på standardpriser. Om du har konfigurerat en databevarandeprincip debiteras du inte när Azure Storage tar bort den gamla mått data. Om du tar bort analysdata debiteras ditt konto för delete-åtgärder.

Den kapacitet som används av tabellerna mått är också fakturerbar: du kan använda följande för att beräkna storleken på den kapacitet som används för att lagra data för mått:

* Om varje timme en tjänst använder varje API i varje tjänst, är cirka 148KB data lagrade i timmen i tabellerna mått transaktion om du har aktiverat både tjänsten och översikt över API-nivå.
* Om varje timme en tjänst använder varje API i varje tjänst, är cirka 12KB data lagrade i timmen i tabellerna mått transaktion om du har aktiverat bara servicenivå som är sammanfattning.
* Tabellen kapacitet för BLOB har två rader läggs varje dag (förutsatt att användaren har valt i loggar): Detta innebär att varje dag ökar storleken på den här tabellen med upp till ungefär 300 byte.

## <a name="next-steps"></a>Nästa steg
[Aktivera loggning och åtkomst till loggdata](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
