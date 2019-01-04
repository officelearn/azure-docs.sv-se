---
title: Aktiverar du lagringsmått i Azure portal | Microsoft Docs
description: Så här aktiverar du lagringsmått för tjänsterna Blob, kö, tabell och fil
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 3e6a11a8c225afb220f290cee2db39c36750b401
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632152"
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Aktivera Azure Storage-mätvärden och visa måttdata
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Översikt
Storage-mått är aktiverat som standard när du skapar ett nytt lagringskonto. Du kan konfigurera övervakning via den [Azure-portalen](https://portal.azure.com) eller Windows PowerShell eller via programmering via någon av storage-klientbibliotek.

Du kan konfigurera en kvarhållningsperiod för mätvärden: den här perioden avgör för hur länge lagring håller för mått och avgifter som du för utrymmet krävs för att lagra dem. Normalt bör du använda en kortare period för minutmått än per timme mått på grund av den betydande extra utrymme som krävs för minutmått. Välj en kvarhållningsperiod så att du har tillräckligt med tid för att analysera data och ladda ned alla mått som du vill behålla för offline-analys och rapportering. Kom ihåg att du debiteras också för att ladda ned mätvärden från ditt lagringskonto.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Så här aktiverar du mått med hjälp av Azure portal
Följ stegen nedan för att aktivera mätvärden i den [Azure-portalen](https://portal.azure.com):

1. Navigera till ditt lagringskonto.
1. Välj **diagnostik** från den **menyn** fönstret.
1. Se till att **Status** är inställd på **på**.
1. Välj mått för de tjänster som du vill övervaka.
1. Ange en bevarandeprincip som anger hur lång tid att spara mätvärden och loggdata.
1. Välj **Spara**.

Den [Azure-portalen](https://portal.azure.com) för närvarande kan du inte konfigurera minutmått i ditt storage-konto, måste du aktivera minutmått med hjälp av PowerShell eller via programmering.

## <a name="how-to-enable-metrics-using-powershell"></a>Så här aktiverar du mått med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan använda PowerShell på den lokala datorn för att konfigurera mätvärden i Storage i ditt storage-konto med hjälp av Azure PowerShell-cmdleten Get-AzStorageServiceMetricsProperty för att hämta de aktuella inställningarna och cmdleten Set-AzStorageServiceMetricsProperty att ändra de aktuella inställningarna.

Cmdletar som styr Lagringsmått använder följande parametrar:

* MetricsType: möjliga värden är timme och minut.
* ServiceType: möjliga värden är Blob, Queue och Table.
* MetricsLevel: möjliga värden är None, tjänst och ServiceAndApi.

Till exempel växlar följande kommando på minutmått för Blob service i ditt storage-kontot med kvarhållning tidsperiod som angetts till fem dagar:

```powershell
Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

Följande kommando hämtar de aktuella per timme mått nivå och kvarhållning dagarna för blob service i standardkontot för lagring:

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Information om hur du konfigurerar finns Azure PowerShell-cmdletar för att arbeta med din Azure-prenumeration och hur du väljer standardkontot för lagring att använda, i: [Hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Så här aktiverar du lagringsmått programmässigt
Följande C#-kodavsnitt visar hur du aktiverar mått och loggning för Blob-tjänsten med hjälp av storage-klientbiblioteket för .NET:

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

## <a name="viewing-storage-metrics"></a>Visa lagringsmått
När du har konfigurerat Storage Analytics mätvärden för att övervaka ditt storage-konto, registrerar Storage Analytics mätvärden i en uppsättning välkända tabeller i ditt lagringskonto. Du kan konfigurera diagram om du vill visa per timme mått i den [Azure-portalen](https://portal.azure.com):

1. Gå till ditt lagringskonto i den [Azure-portalen](https://portal.azure.com).
1. Välj **mått** i den **menyn** fönstret för tjänsten vars mått som du vill visa.
1. Välj **redigera** på diagrammet som du vill konfigurera.
1. I den **redigera diagram** väljer den **tidsintervall**, **diagramtyp**, och de mått som du vill visa i diagrammet.
1. Välj **OK**

Om du vill hämta mått för långsiktig lagring eller analysera dem lokalt, måste du:

* Använd ett verktyg som känner till dessa tabeller och gör att du kan visa och hämta dem.
* Skriv ett anpassat program eller skript för att läsa och lagra tabellerna.

Surfa på storage-verktyg för många från tredje part är medvetna om dessa tabeller och gör att du kan visa dem direkt.
Se [Azure Storage-klientverktyg](storage-explorers.md) en lista över tillgängliga verktyg.

> [!NOTE]
> Från och med version 0.8.0 av den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/), du kan visa och ladda ned tabellerna analytics mått.
>
>

För att komma åt analyser Observera tabeller programmässigt, att analytics-tabellerna inte visas om du visar alla tabeller i ditt lagringskonto. Du kan komma åt dem direkt efter namn eller använda den [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) i klientbiblioteket för .NET för att fråga tabellnamn.

### <a name="hourly-metrics"></a>Per timme mått
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Minutmått
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Kapacitet
* $MetricsCapacityBlob

Du hittar fullständig information om scheman för dessa tabeller på [Schema över Måttabeller i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx). Raderna exemplet nedan visar endast en delmängd av kolumnerna som är tillgängliga, men visar några viktiga funktioner i sätt Lagringsmått sparar de här måtten:

| PartitionKey | RowKey | Tidsstämpel | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Tillgänglighet | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |användaren. Alla |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |användaren. QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |användaren. QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

I det här exemplet minutmått data använder Partitionsnyckeln tiden med minut upplösning. Radnyckeln identifierar vilken typ av information som lagras i raden. Radnyckeln består av två typer av information, vilken åtkomst och typ av begäran:

* Åtkomsttypen är användaren eller systemet, där användaren avser alla användarförfrågningar till storage-tjänsten och system som refererar till begäranden som görs av Storage Analytics.
* Typ av begäran är antingen alla som innebär att det är en sammanfattande rad eller identifieras, till exempel QueryEntity eller UpdateEntity specifika API: et.

Exempeldata ovan visar alla poster för en minut (med början 11:00:00), så att antalet QueryEntities begäranden plus antalet QueryEntity begär plus antalet UpdateEntity manuellt lägga till upp till sju, vilket är det totala antalet visas på raden för användaren: All. På samma sätt du kan härleda slutpunkt till slutpunkt genomsnittlig svarstid 104.4286 på raden för användaren: All genom att beräkna ((143.8 * 5) + 3 + 9) / 7.

Tänk på att **Blob per timme måttinställningar** tillämpas på både **Blob kapacitet mått** ($MetricsCapacityBlob) och **Blob transaktionsmått** ($ MetricsHourPrimaryTransactionsBlob). Båda är aktiverade eller inaktiverade tillsammans och använda samma bevarandeprincip.

## <a name="metrics-alerts"></a>Aviseringar för mått
Du bör du överväga att konfigurera aviseringar i den [Azure-portalen](https://portal.azure.com) så Lagringsmått kan automatiskt meddela dig om viktiga ändringar i beteendet för storage-tjänster. Om du använder en storage explorer-verktyget för att ladda ned den här metrics data i en avgränsad format, kan du använda Microsoft Excel för att analysera data. Se [Azure Storage-klientverktyg](storage-explorers.md) en lista över tillgängliga storage explorer-verktyg. Du kan konfigurera aviseringar i den **Aviseringsregler** fönstret tillgänglig under **övervakning** i rutan för Storage-menyn.

> [!IMPORTANT]
> Det kan finnas en fördröjning mellan en händelse för lagring och när motsvarande per timme eller minut statistikdata registreras. När du loggar minutmått kan flera minuters data skrivas på samma gång. Transaktioner från tidigare minuter kan sedan aggregeras till transaktionen för den aktuella minuten. När detta sker kanske alert-tjänsten inte har alla tillgängliga måttdata för det konfigurerade aviseringarna intervallet, vilket kan leda till aviseringar aktiveringen oväntat.
>

## <a name="accessing-metrics-data-programmatically"></a>Komma åt mätvärden via programmering
Följande lista visar exempel C#-kod som har åtkomst till minutmått för ett antal minuter och visar resultatet i ett konsolfönster. Azure Storage-biblioteket version 4 med CloudAnalyticsClient-klass som förenklar åtkomst till tabellerna mätvärden i storage används.

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

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Vilka avgifter du medför när du aktiverar mätvärden i storage?
Skriva begäranden om att skapa tabellentiteter för mått debiteras enligt standardpriser gäller för alla Azure Storage-åtgärder.

Läs- och delete-begäranden av en klient till måttdata är också faktureringsbara standardpriserna. Om du har konfigurerat en databevarandeprincip kan debiteras du inte när Azure Storage tar bort gamla mätvärden. Om du tar bort analysdata debiteras ditt konto för delete-åtgärder.

Den kapacitet som används av tabellerna mått är också faktureringsbara: du kan använda följande för att uppskatta hur mycket kapacitet som används för att lagra måttdata:

* Om varje timme som en tjänst använder alla API: er i varje tjänst, är cirka 148KB data lagrad varje timme i transaktionsregister mått om du har aktiverat både tjänsten och API-nivå sammanfattning.
* Om varje timme som en tjänst använder alla API: er i varje tjänst, är cirka 12KB data lagrad varje timme i transaktionsregister mått om du har aktiverat bara servicenivå som är sammanfattning.
* Tabellen kapacitet för BLOB har två rader har lagts till varje dag (förutsatt att användaren har valt i för loggar): Detta innebär att varje dag ökar storleken på den här tabellen av upp till ungefär 300 byte.

## <a name="next-steps"></a>Nästa steg
[Aktivera lagring loggning och komma åt loggdata](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
