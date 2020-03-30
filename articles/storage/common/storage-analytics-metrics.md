---
title: Azure Storage analytics-mått (Klassisk)
description: Lär dig hur du använder mått i Azure Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268410"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage analytics-mått (Klassisk)

Storage Analytics kan lagra mått som innehåller aggregerad transaktionsstatistik och kapacitetsdata om begäranden till en lagringstjänst. Transaktioner rapporteras både på API-åtgärdsnivå och på lagringstjänstnivå, och kapacitet rapporteras på lagringstjänstnivå. Måttdata kan användas för att analysera användning av lagringstjänster, diagnostisera problem med begäranden som görs mot lagringstjänsten och för att förbättra prestanda för program som använder en tjänst.  

 Storage Analytics-mått är aktiverade som standard för nya lagringskonton. Du kan konfigurera mått i [Azure-portalen](https://portal.azure.com/). Mer information finns [i Övervaka ett lagringskonto i Azure-portalen](/azure/storage/storage-monitor-storage-account). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Använd åtgärderna Ange tjänstegenskaper för att aktivera Storage Analytics för varje tjänst.  

> [!NOTE]
> Storage Analytics-mått är tillgängliga för Blob-, Kö-, Table- och File-tjänsterna.
> Lagring Analytics-mått är nu klassiska mått. Microsoft rekommenderar att du använder [lagringsmått i Azure Monitor i](storage-metrics-in-azure-monitor.md) stället för Storage Analytics-mått.

## <a name="transaction-metrics"></a>Transaktionsmått  
 En robust uppsättning data registreras med tim- eller minutintervall för varje lagringstjänst och begärd API-åtgärd, inklusive ingående/utgående, tillgänglighet, fel och kategoriserade procentandelar för begäran. Du kan se en fullständig lista över transaktionsinformationen i avsnittet [Lagringsanalysmått Tabell schema.](/rest/api/storageservices/storage-analytics-metrics-table-schema)  

 Transaktionsdata registreras på två nivåer – tjänstnivån och API-åtgärdsnivån. På servicenivå skrivs statistik som sammanfattar alla begärda API-åtgärder till en tabellentitet varje timme även om inga begäranden gjordes till tjänsten. På API-åtgärdsnivå skrivs statistik endast till en entitet om åtgärden begärdes inom den timmen.  

 Om du till exempel utför en **GetBlob-åtgärd** på din Blob-tjänst loggar Storage Analytics Metrics begäran och inkluderar den i de aggregerade data för både Blob-tjänsten och **GetBlob-åtgärden.** Om ingen **GetBlob-åtgärd** begärs under timmen skrivs dock inte en entitet till *$MetricsTransactionsBlob* för den operationen.  

 Transaktionsmått registreras för både användarbegäranden och begäranden som gjorts av Storage Analytics själv. Begäranden från Storage Analytics för att skriva loggar och tabellentiteter registreras till exempel.

## <a name="capacity-metrics"></a>Kapacitetsmått  

> [!NOTE]
>  För närvarande är kapacitetsmått endast tillgängliga för Blob-tjänsten.

 Kapacitetsdata registreras dagligen för ett lagringskontos Blob-tjänst och två tabellentiteter skrivs. En entitet tillhandahåller statistik för användardata och `$logs` den andra innehåller statistik om blob-behållaren som används av Storage Analytics. Tabellen *$MetricsCapacityBlob* innehåller följande statistik:  

- **Kapacitet**: Mängden lagringsutrymme som används av lagringskontots Blob-tjänst, i byte.  
- **ContainerCount**: Antalet blob-behållare i lagringskontots Blob-tjänst.  
- **ObjectCount**: Antalet bekräftade och oengagerade block- eller sidblobar i lagringskontots Blob-tjänst.  

  Mer information om kapacitetsmåtten finns i [Tabellschema för lagringsanalysmått](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Hur mått lagras  

 Alla måttdata för var och en av lagringstjänsterna lagras i tre tabeller som är reserverade för den tjänsten: en tabell för transaktionsinformation, en tabell för minuttransaktionsinformation och en annan tabell för kapacitetsinformation. Transaktions- och minuttransaktionsinformation består av begärande- och svarsdata, och kapacitetsinformation består av lagringsdata. Timmått, minutmått och kapacitet för ett lagringskontos Blob-tjänst kan nås i tabeller som namnges enligt beskrivningen i följande tabell.  

|Nivå för mått|Tabellnamn|Stöds för versioner|  
|-------------------|-----------------|----------------------------|  
|Timmått, primär plats|- $MetricsTransactionsBlob<br />- $MetricsTransactionsTable<br />- $MetricsTransactionsQueue|Versioner före 2013-08-15 endast. Även om dessa namn fortfarande stöds rekommenderar vi att du växlar till att använda tabellerna nedan.|  
|Timmått, primär plats|- $MetricsHourPrimaryTransactionsBlob<br />- $MetricsHourPrimaryTransactionsTable<br />- $MetricsHourPrimaryTransactionsQueue<br />- $MetricsHourPrimaryTransactionsFile|Alla versioner. Stöd för filtjänstmått är endast tillgängligt i version 2015-04-05 och senare.|  
|Minutmått, primär plats|- $MetricsMinutePrimaryTransactionsBlob<br />- $MetricsMinutePrimaryTransactionsTable<br />- $MetricsMinutePrimaryTransactionsQueue<br />- $MetricsMinutePrimaryTransactionsFile|Alla versioner. Stöd för filtjänstmått är endast tillgängligt i version 2015-04-05 och senare.|  
|Timmått, sekundär plats|- $MetricsHourSecondaryTransactionsBlob<br />- $MetricsHourSecondaryTransactionsTable<br />- $MetricsHourSecondaryTransactionsQueue|Alla versioner. Geo redundant replikering för läsåtkomst måste vara aktiverad.|  
|Minutmått, sekundär plats|- $MetricsMinuteSecondaryTransactionsBlob<br />- $MetricsMinuteSecondaryTransactionsTable<br />- $MetricsMinuteSecondaryTransactionsQueue|Alla versioner. Geo redundant replikering för läsåtkomst måste vara aktiverad.|  
|Kapacitet (endast Blob-tjänst)|$MetricsCapacityBlob|Alla versioner.|  

 Dessa tabeller skapas automatiskt när Storage Analytics är aktiverat för en slutpunkt för lagringstjänster. De nås via namnområdet för lagringskontot, `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`till exempel: . Måtttabellerna visas inte i en listningsåtgärd och måste nås direkt via tabellnamnet.  

## <a name="enable-metrics-using-the-azure-portal"></a>Aktivera mått med Azure-portalen
Följ dessa steg för att aktivera mått i [Azure-portalen:](https://portal.azure.com)

1. Navigera till ditt lagringskonto.
1. Välj **Diagnostikinställningar (klassisk)** i **menyfönstret.**
1. Kontrollera att **status** är inställt **på På**.
1. Välj mått för de tjänster som du vill övervaka.
1. Ange en bevarandeprincip för att ange hur länge mått och loggdata ska behållas.
1. Välj **Spara**.

[Azure-portalen](https://portal.azure.com) gör det för närvarande inte möjligt att konfigurera minutmått i ditt lagringskonto. Du måste aktivera minutmått med PowerShell eller programmässigt.

## <a name="enable-storage-metrics-using-powershell"></a>Aktivera lagringsmått med PowerShell  
Du kan använda PowerShell på din lokala dator för att konfigurera lagringsmått i ditt lagringskonto med hjälp av Azure PowerShell cmdlet **Get-AzStorageServiceMetricsProperty** för att hämta de aktuella inställningarna och cmdlet **Set-AzStorageServiceMetricsProperty** för att ändra de aktuella inställningarna.  

Cmdlets som styr lagringsmått använder följande parametrar:  

* **ServiceType**, möjligt värde är **Blob,** **Kö,** **Tabell**och **Arkiv**.
* **MetricsType**, möjliga värden är **Timme** och **Minut**.  
* **MåttNivå**, möjliga värden är:
* **Ingen**: Stänger av övervakningen.
* **Tjänst**: Samlar in mått som ingående/utgående, tillgänglighet, svarstid och framgångsprocent, som aggregeras för blob-, kö-, tabell- och filtjänster.
* **ServiceAndApi**: Förutom servicemåtten samlar samma uppsättning mått för varje lagringsåtgärd i Azure Storage-tjänst-API:et.

Följande kommando växlar till exempel på minutmått för blob-tjänsten i ditt lagringskonto med kvarhållningsperioden inställd på fem dagar: 

> [!NOTE]
> Det här kommandot förutsätter att du har loggat in på din Azure-prenumeration med kommandot. `Connect-AzAccount`

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Ersätt `<resource-group-name>` platshållarvärdet med namnet på resursgruppen.
        
* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.



Följande kommando hämtar den aktuella timmåttsnivån och kvarhållningsdagarna för blob-tjänsten i ditt standardlagringskonto:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Information om hur du konfigurerar Azure PowerShell-cmdletar för att fungera med din Azure-prenumeration och hur du väljer det standardlagringskonto som ska användas finns i: [Installera och konfigurera Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Aktivera lagringsmått programmässigt  
Förutom att använda Azure-portalen eller Azure PowerShell-cmdlets för att styra lagringsmått kan du också använda en av Azure Storage API:er. Om du till exempel använder ett .NET-språk kan du använda storage-klientbiblioteket.  

Klasserna **CloudBlobClient,** **CloudQueueClient**, **CloudTableClient**och **CloudFileClient** har alla metoder som **SetServiceProperties** och **SetServicePropertiesAsync** som tar ett **ServiceProperties-objekt** som en parameter. Du kan använda **ServiceProperties-objektet** för att konfigurera lagringsmått. Följande C#-kodavsnitt visar till exempel hur du ändrar måttnivån och kvarhållningsdagarna för timkömåtten:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Mer information om hur du använder ett .NET-språk för att konfigurera lagringsmått finns i [Storage Client Library för .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Allmän information om hur du konfigurerar lagringsmått med REST API finns i [Aktivera och konfigurera Lagringsanalys](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Visa lagringsmått  
När du har konfigurerat Storage Analytics-mått för att övervaka ditt lagringskonto registrerar Storage Analytics måtten i en uppsättning välkända tabeller i ditt lagringskonto. Du kan konfigurera diagram för att visa timmått i [Azure-portalen:](https://portal.azure.com)

1. Navigera till ditt lagringskonto i [Azure-portalen](https://portal.azure.com).
1. Välj **Mått (klassiskt)** i **menybladet** för den tjänst vars mått du vill visa.
1. Klicka på det diagram som du vill konfigurera.
1. I bladet **Redigera diagram** väljer du typen **Tidsintervall,** **Diagramtyp**och de mått som du vill ska visa i diagrammet.

I avsnittet **Övervakning (klassiskt)** i menybladet för ditt lagringskonto i Azure-portalen kan du konfigurera [aviseringsregler,](#metrics-alerts)till exempel skicka e-postaviseringar för att meddela dig när ett visst mått når ett visst värde.

Om du vill hämta måtten för långsiktig lagring eller analysera dem lokalt måste du använda ett verktyg eller skriva en kod för att läsa tabellerna. Du måste hämta minutmåtten för analys. Tabellerna visas inte om du listar alla tabeller i ditt lagringskonto, men du kan komma åt dem direkt med namn. Många lagringsverktyg är medvetna om dessa tabeller och gör att du kan visa dem direkt (se [Azure Storage Client Tools](/azure/storage/storage-explorers) för en lista över tillgängliga verktyg).

||||  
|-|-|-|  
|**Statistik**|**Tabellnamn**|**Obs!**|  
|Timmått|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|I versioner före 2013-08-15 kallades dessa tabeller:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Mått för filtjänsten är tillgängliga från och med version 2015-04-05.|  
|Minutmått|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Det går bara att aktivera med PowerShell eller programmässigt.<br /><br /> Mått för filtjänsten är tillgängliga från och med version 2015-04-05.|  
|Kapacitet|$MetricsCapacityBlob|Endast blob-tjänsten.|  

Du hittar fullständig information om scheman för dessa tabeller på [Storage Analytics Metrics Table Schema](/rest/api/storageservices/storage-analytics-metrics-table-schema). Exempelraderna nedan visar bara en delmängd av de kolumner som är tillgängliga, men illustrerar några viktiga funktioner i hur lagringsmått sparar dessa mått:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Tidsstämpel**|**TotalRequests**|**TotaltFakturerbara Begärande**|**TotalIngress**|**TotalEgress**|**Tillgänglighet**|**AverageE2ELatency**|**AverageServerLatency**|**Procentsug**|  
|20140522T1100|användare. Alla|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|användare. Frågeentiteter|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|användare. QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|användare. UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

I det här exemplet minut mått data använder partitionsnyckeln tiden vid minutupplösning. Radnyckeln identifierar vilken typ av information som lagras i raden och den består av två informationsdelar, åtkomsttypen och typen för begäran:  

-   Åtkomsttypen är antingen **användare** eller **system**, där **användaren** refererar till alla användarbegäranden till lagringstjänsten och **systemet** refererar till begäranden från Storage Analytics.  

-   Typen av begäran är antingen **allt** i vilket fall det är en sammanfattningsrad eller identifierar det specifika API:et som **QueryEntity** eller **UpdateEntity**.  

Exempeldata ovan visar alla poster för en enda minut (från 11:00), så antalet förfrågningar om **frågeentiteter** plus antalet **QueryEntity-begäranden** plus antalet **UpdateEntity-begäranden** summerar upp till sju, vilket är summan som visas på **raden user:All.** På samma sätt kan du härleda den genomsnittliga svarstiden från till 104.4286 på **användaren:Alla** raden genom att beräkna ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Aviseringar om mått
Du bör överväga att konfigurera aviseringar i [Azure-portalen](https://portal.azure.com) så att du automatiskt meddelas om viktiga ändringar i beteendet för dina lagringstjänster. Om du använder ett lagringsutforskarenverktyg för att hämta dessa måttdata i avgränsat format kan du använda Microsoft Excel för att analysera data. Se [Azure Storage Client Tools](/azure/storage/storage-explorers) för en lista över tillgängliga verktyg för lagringsutforskare. Du kan konfigurera aviseringar i **bladet Alert (klassisk),** som är tillgängliga under **Övervakning (klassisk)** i menybladet För lagringskonto.

> [!IMPORTANT]
> Det kan finnas en fördröjning mellan en lagringshändelse och när motsvarande tim- eller minutmåttdata registreras. När det gäller minutmått kan flera minuter data skrivas på en gång. Detta kan leda till att transaktioner från tidigare minuter aggregeras till transaktionen för den aktuella minuten. När detta inträffar kanske aviseringstjänsten inte har alla tillgängliga måttdata för det konfigurerade varningsintervallet, vilket kan leda till att aviseringar aktiveras oväntat.
>

## <a name="accessing-metrics-data-programmatically"></a>Komma åt mätvärden programmässigt  
Följande lista visar exempel på C#-kod som kommer åt minutmåtten i en rad minuter och visar resultaten i ett konsolfönster. Kodexemplet använder Azure Storage Client Library version 4.x eller senare, som innehåller klassen **CloudAnalyticsClient** som förenklar åtkomsten till måtttabellerna i lagring.  

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

## <a name="billing-on-storage-metrics"></a>Fakturering på lagringsmått  
Skrivbegäranden för att skapa tabellentiteter för mått debiteras enligt de standardpriser som gäller för alla Azure Storage-åtgärder.  

Läsa och ta bort begäranden om måttdata från en klient kan också faktureras till standardpriser. Om du har konfigurerat en datalagringsprincip debiteras du inte när Azure Storage tar bort gamla måttdata. Men om du tar bort analysdata debiteras ditt konto för borttagningsåtgärderna.  

Den kapacitet som används av måtttabellerna kan också faktureras. Du kan använda följande för att uppskatta mängden kapacitet som används för att lagra måttdata:  

-   Om varje timme en tjänst använder varje API i varje tjänst, lagras ungefär 148KB data varje timme i måtttransaktionstabellerna om du har aktiverat sammanfattningen på både tjänst- och API-nivå.  
-   Om en tjänst inom varje timme använder alla API i tjänsten lagras ungefär 12 TB data varje timme i måtttransaktionstabellerna om du bara har aktiverat sammanfattning på servicenivå.  
-   Kapacitetstabellen för blobbar har två rader tillagda varje dag, förutsatt att du har valt loggar. Detta innebär att varje dag ökar storleken på den här tabellen med upp till cirka 300 byte.

## <a name="next-steps"></a>Nästa steg
* [Så här övervakar du ett lagringskonto](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Tabellschema för lagringsanalysmått](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Loggade åtgärder och statusmeddelanden för lagringsanalys](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Loggning för lagringsanalys](storage-analytics-logging.md)
