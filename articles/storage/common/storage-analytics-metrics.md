---
title: Azure Storage analytics-mått (klassisk)
description: Lär dig hur du använder mått i Azure Storage.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: b35d3d22ce154420c9099143894688389e8af420
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2019
ms.locfileid: "58078048"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage analytics-mått (klassisk)

Lagringsanalys kan lagra mätvärden som innehåller aggregerad Transaktionsstatistik och kapacitetsdata om förfrågningar till en lagringstjänst. Transaktioner rapporteras på både den API-åtgärdsnivå samt på tjänstnivå lagring och kapacitet rapporteras på tjänstnivå lagring. Måttdata kan användas för att analysera användning av storage-tjänsten, diagnostisera problem med begäranden som görs mot tjänsten storage och förbättra prestanda för program som använder en tjänst.  

 Mätvärden i Storage Analytics är aktiverade som standard för nya storage-konton. Du kan konfigurera mått i den [Azure-portalen](https://portal.azure.com/); mer information finns i [övervaka ett lagringskonto i Azure-portalen](/azure/storage/storage-monitor-storage-account). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Aktivera Storage Analytics för varje tjänst med hjälp av åtgärderna ange tjänstegenskaper.  

> [!NOTE]
> Mätvärden i Storage Analytics är tillgängliga för tjänsterna Blob, kö, tabell och fil.
> Mätvärden i Storage Analytics är nu klassisk mått. Microsoft rekommenderar att du använder [Storage-mått i Azure Monitor](/azure/storage/common/storage-metrics-in-azure-monitor.md) i stället för Storage Analytics mätvärden.

## <a name="transaction-metrics"></a>Transaktionsmått  
 En stabil uppsättning data registreras med timvis eller minuters intervall för varje lagringstjänst och den begärda API-åtgärden, inklusive ingående/utgående trafik, tillgänglighet, fel, och kategoriseras begäran procenttal. Du kan se en fullständig lista över transaktionsuppgifter i den [Schema över Måttabeller i Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema.md) avsnittet.  

 Transaktionsdata registreras på två nivåer – servicenivån och API-åtgärdsnivå. På servicenivån begärda statistik som sammanfattar alla API-åtgärder skrivs till en tabellentitet varje timme även om inga ansökningar har gjorts till tjänsten. På åtgärdsnivå API skrivs statistik enbart till en entitet om åtgärden begärdes den timmen.  

 Exempel: Om du utför en **GetBlob** åtgärden på din Blob-tjänst, mätvärden i Storage Analytics kommer loggar begäran och inkludera den i den sammanställda data för både Blob service samt de **GetBlob** åtgärden. Men om ingen **GetBlob** åtgärd har begärts under timmen, en entitet kommer inte att skriva till den *$MetricsTransactionsBlob* för den åtgärden.  

 Transaktionsmått registreras för både användarförfrågningar och begäranden som görs av Lagringsanalys själva. Till exempel registreras begäranden av Storage Analytics för att skriva loggar och tabellenheter.

## <a name="capacity-metrics"></a>Kapacitet mått  

> [!NOTE]
>  Kapacitet är för närvarande endast tillgängliga för Blob-tjänsten.

 Registreras kapacitetsdata varje dag för ett lagringskontos Blob service och två tabellenheter skrivs. En entitet innehåller statistik för användardata och den andra innehåller statistik om den `$logs` blob-behållare som används av Storage Analytics. Den *$MetricsCapacityBlob* tabell innehåller följande statistik:  

- **Kapacitet**: Mängden lagringsutrymme som används av lagringskontots Blob service i byte.  
- **ContainerCount**: Antal blob-behållare i lagringskontots Blob service.  
- **ObjectCount**: Antal allokerade och ogenomförda block- eller sidtyp blobar i lagringskontots Blob service.  

  Läs mer om kapacitetsmåtten [Schema över Måttabeller i Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema.md).  

## <a name="how-metrics-are-stored"></a>Hur mått lagras  

 Alla mätvärden för var och en av lagringstjänsterna lagras i tre tabeller som är reserverade för tjänsten: en tabell för transaktionsinformation, en tabell för minut transaktionsinformation och en annan tabell för kapacitetsinformation. Transaktionen och minut transaktionsinformation består av data för begäran och svar och kapacitetsinformation består av användningsdata för lagring. Timmått minutmått och kapacitet för ett storage-kontots Blob-tjänsten kan nås i tabeller som namnges enligt beskrivningen i följande tabell.  

|Mått-nivå|Tabellnamn|Stöd för versioner|  
|-------------------|-----------------|----------------------------|  
|Mätvärden för varje timme, primär plats|-   $MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Versioner före 2013-08-15 endast. Dessa namn finns fortfarande stöd för rekommenderar vi att du istället använda registren nedan.|  
|Mätvärden för varje timme, primär plats|-   $MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Alla versioner. Stöd för filen tjänstmått är endast tillgängliga i version 2015-04-05 och senare.|  
|Minutmått, primär plats|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Alla versioner. Stöd för filen tjänstmått är endast tillgängliga i version 2015-04-05 och senare.|  
|Mätvärden för varje timme, sekundär plats|-   $MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Alla versioner. Read-access geo-redundant replikering måste aktiveras.|  
|Minutmått, sekundär plats|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Alla versioner. Read-access geo-redundant replikering måste aktiveras.|  
|Kapacitet (endast Blob-tjänst)|$MetricsCapacityBlob|Alla versioner.|  

 Dessa tabeller skapas automatiskt när Storage Analytics är aktiverat för en slutpunkt för lagring. De kan nås via namnområdet för storage-konto, till exempel: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Mått-tabellerna visas inte i en liståtgärden och måste kunna nås direkt via tabellens namn.  

## <a name="enable-metrics-using-the-azure-portal"></a>Aktivera mätvärden via Azure portal
Följ stegen nedan för att aktivera mätvärden i den [Azure-portalen](https://portal.azure.com):

1. Navigera till ditt lagringskonto.
1. Välj **diagnostikinställningar (klassisk)** från den **menyn** fönstret.
1. Se till att **Status** är inställd på **på**.
1. Välj mått för de tjänster som du vill övervaka.
1. Ange en bevarandeprincip som anger hur lång tid att spara mätvärden och loggdata.
1. Välj **Spara**.

Den [Azure-portalen](https://portal.azure.com) för närvarande kan du inte konfigurera minutmått i ditt storage-konto, måste du aktivera minutmått med hjälp av PowerShell eller via programmering.

> [!NOTE]
>  Observera att Azure-portalen för närvarande kan du inte konfigurera minutmått i ditt storage-konto. Du måste aktivera minutmått med hjälp av PowerShell eller via programmering.  

## <a name="enable-storage-metrics-using-powershell"></a>Aktivera mätvärden i Storage med hjälp av PowerShell  
Du kan använda PowerShell på den lokala datorn för att konfigurera mätvärden i Storage i ditt storage-konto med hjälp av Azure PowerShell-cmdleten **Get-AzureStorageServiceMetricsProperty** att hämta de aktuella inställningarna och cmdleten  **Set-AzureStorageServiceMetricsProperty** att ändra de aktuella inställningarna.  

Cmdletar som styr Lagringsmått använder följande parametrar:  

* **ServiceType**, möjliga värde är **Blob**, **kö**, **tabell**, och **filen**.
* **MetricsType**, möjliga värden är **timme** och **minut**.  
* **MetricsLevel**, möjliga värden är:
* **Ingen**: Om du inaktiverar övervakning.
* **Tjänsten**: Samlar in mätvärden, till exempel ingående/utgående trafik, tillgänglighet, svarstid och procentuellt antal lyckade sökningar, som sammanställs för blob, kö, tabell och Filtjänster.
* **ServiceAndApi**: Samlar in samma uppsättning mått för varje lagringsåtgärd i API: et för Azure Storage-tjänsten utöver Service-mått.

Till exempel växlar följande kommando på minutmått för blob service i ditt storage-kontot med kvarhållning tidsperiod som angetts till fem dagar:  

```  
Set-AzureStorageServiceMetricsProperty -MetricsType Minute   
-ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5  
```  

Följande kommando hämtar de aktuella per timme mått nivå och kvarhållning dagarna för blob service i standardkontot för lagring:  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour   
-ServiceType Blob  
```  

Information om hur du konfigurerar finns Azure PowerShell-cmdletar för att arbeta med din Azure-prenumeration och hur du väljer standardkontot för lagring att använda, i: [Hur du installerar och konfigurerar du Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Aktivera Storage-mätvärden via programmering  
Förutom att använda Azure portal eller Azure PowerShell-cmdletar för att styra mätvärden i Storage kan använda du också en av API: erna för Azure Storage. Du kan till exempel använda Lagringsklientbiblioteket om du använder ett .NET-språk.  

Klasserna **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**, och **CloudFileClient** alla har metoder, till exempel  **SetServiceProperties** och **SetServicePropertiesAsync** som tar en **ServiceProperties** objektet som en parameter. Du kan använda den **ServiceProperties** objekt för att konfigurera Storage-mått. Till exempel följande C# kodfragmentet visar hur du ändrar mått nivå och kvarhållning dagar för varje timme kö mätvärden:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Läs mer om hur du använder ett .NET-språk för att konfigurera Lagringsmått [Lagringsklientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Allmän information om hur du konfigurerar mätvärden i Storage med hjälp av REST-API finns i [aktivera och konfigurera Lagringsanalys](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics.md).  

##  <a name="viewing-storage-metrics"></a>Visa lagringsmått  
När du har konfigurerat Storage Analytics mätvärden för att övervaka ditt storage-konto, registrerar Storage Analytics mätvärden i en uppsättning välkända tabeller i ditt lagringskonto. Du kan konfigurera diagram om du vill visa per timme mått i den [Azure-portalen](https://portal.azure.com):

1. Gå till ditt lagringskonto i den [Azure-portalen](https://portal.azure.com).
1. Välj **mått (klassisk)** i den **menyn** bladet för tjänsten vars mått som du vill visa.
1. Klicka på diagrammet som du vill konfigurera.
1. I den **redigera diagram** bladet och välja den **tidsintervall**, **diagramtyp**, och de mått som du vill visa i diagrammet.

I den **övervakning (klassisk)** avsnittet av lagringskontots meny bladet i Azure-portalen kan du konfigurera [Aviseringsregler](#metrics-alerts), till exempel skicka e-postmeddelande aviseringar som meddelar dig när ett specifikt mått når en visst värde.

Om du vill hämta mått för långsiktig lagring eller analysera dem lokalt, måste du använda ett verktyg eller skriva kod för att läsa tabellerna. Du måste hämta minutmått för analys. Tabellerna visas inte om du lista alla tabeller i ditt storage-konto, men du kan komma åt dem direkt efter namn. Många surfa på storage-verktyg är medvetna om dessa tabeller och gör att du kan visa dem direkt (se [Azure Storage-klientverktyg](/azure/storage/storage-explorers) en lista över tillgängliga verktyg).

||||  
|-|-|-|  
|**Mått**|**Tabellnamn**|**Anteckningar**|  
|Per timme mått|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|I tidigare versioner än 2013-08-15 kallas tabellerna:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Mått för tjänsten är tillgängliga från och med version 2015-04-05.|  
|Minutmått|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Kan bara aktiveras med hjälp av PowerShell eller via programmering.<br /><br /> Mått för tjänsten är tillgängliga från och med version 2015-04-05.|  
|Kapacitet|$MetricsCapacityBlob|BLOB-tjänsten.|  

Du hittar fullständig information om scheman för dessa tabeller på [Schema över Måttabeller i Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema.md). Raderna exemplet nedan visar endast en delmängd av kolumnerna som är tillgängliga, men visar några viktiga funktioner i sätt Lagringsmått sparar de här måtten:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**partitionKey**|**RowKey**|**Tidsstämpel**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Tillgänglighet**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|användaren. Alla|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

I det här exemplet minutmått data använder Partitionsnyckeln tiden med minut upplösning. Radnyckeln identifierar typ av information som lagras på raden och detta består av två typer av information, vilken åtkomst och typ av begäran:  

-   Åtkomsttypen är antingen **användaren** eller **system**, där **användaren** avser alla användarförfrågningar till storage-tjänsten och **system** refererar till begäranden som görs av Storage Analytics.  

-   Typ av begäran är antingen **alla** då det är en sammanfattande rad, eller den identifierar den specifika API som **QueryEntity** eller **UpdateEntity**.  

Exempeldata ovan visar alla poster för en minut (med början kl. 11:00), så antalet **QueryEntities** begär plus antalet **QueryEntity** begär plus antalet  **UpdateEntity** begäranden lägga till upp till sju, vilket är det totala antalet visas på den **användare: All** raden. På samma sätt du kan härleda den genomsnittliga svarstiden för slutpunkt till slutpunkt 104.4286 på den **användare: All** raden genom att beräkna ((143.8 * 5) + 3 + 9) / 7.  

## <a name="metrics-alerts"></a>Aviseringar för mått
Du bör du överväga att konfigurera aviseringar i den [Azure-portalen](https://portal.azure.com) så att du kommer att automatiskt meddelas om viktiga ändringar i beteendet för storage-tjänster. Om du använder en storage explorer-verktyget för att ladda ned den här metrics data i en avgränsad format, kan du använda Microsoft Excel för att analysera data. Se [Azure Storage-klientverktyg](/azure/storage/storage-explorers) en lista över tillgängliga storage explorer-verktyg. Du kan konfigurera aviseringar i den **avisering (klassisk)** bladet tillgänglig under **övervakning (klassisk)** i bladet menyn för Lagringskontot.

> [!IMPORTANT]
> Det kan finnas en fördröjning mellan en händelse för lagring och när motsvarande per timme eller minut statistikdata registreras. När det gäller minutmått, kan flera minuters data skrivas på samma gång. Detta kan leda till transaktioner från tidigare minuter som ska aggregeras i transaktionen för den aktuella minuten. När detta sker kanske alert-tjänsten inte har alla tillgängliga måttdata för det konfigurerade aviseringarna intervallet, vilket kan leda till aviseringar aktiveringen oväntat.
>

## <a name="accessing-metrics-data-programmatically"></a>Komma åt mätvärden via programmering  
Följande lista visar exempel C#-kod som har åtkomst till minutmått för ett antal minuter och visar resultatet i ett konsolfönster. Kodexemplet använder Azure Storage-klientbiblioteket version 4.x eller senare, som innehåller den **CloudAnalyticsClient** klass som förenklar åtkomst till tabellerna mätvärden i storage.  

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
     // (StartsWith is not supported using LINQ with Azure Table storage)  
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

## <a name="billing-on-storage-metrics"></a>Faktureringen för storage-mått  
Skriva begäranden om att skapa tabellentiteter för mått debiteras enligt standardpriser gäller för alla Azure Storage-åtgärder.  

Läs- och delete-begäranden av måttdata för en klient är också faktureringsbara standardpriserna. Om du har konfigurerat en databevarandeprincip kan debiteras du inte när Azure Storage tar bort gamla mätvärden. Om du tar bort analysdata debiteras ditt konto för delete-åtgärder.  

Den kapacitet som används av tabellerna mått är också faktureringsbara. Du kan använda följande för att uppskatta hur mycket kapacitet som används för att lagra måttdata:  

-   Om varje timme som en tjänst använder alla API: er i varje tjänst, är cirka 148KB data lagrad varje timme i transaktionsregister mått om du har aktiverat båda och API-tjänstnivå sammanfattning.  
-   Alla API: er i tjänsten använder sig av en tjänst om inom varje timme, och sedan cirka 12KB data lagras varje timme i transaktionsregister mått om du har aktiverat bara servicenivå sammanfattning.  
-   I tabellen över kapacitet för BLOB har två rader har lagts till varje dag, förutsatt att du har valt in för loggar. Detta innebär att varje dag, ökar storleken på den här tabellen av upp till ungefär 300 byte.

## <a name="next-steps"></a>Nästa steg
* [Så här övervakar du ett Storage-konto](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schema över Måttabeller i Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema.md)   
* [Lagringsanalys loggade åtgärder och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md)   
* [Loggningen i Storage Analytics](storage-analytics-logging.md)
