---
title: Azure Storage analys mått (klassisk)
description: Lär dig hur du använder mått i Azure Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514773"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage analys mått (klassisk)

Lagringsanalys kan lagra mått som innehåller aggregerad transaktions statistik och kapacitets data om begär anden till en lagrings tjänst. Transaktioner rapporteras både på API-nivå och på lagrings tjänst nivå, och kapaciteten rapporteras på lagrings tjänst nivå. Mät data kan användas för att analysera användningen av lagrings tjänster, diagnostisera problem med begär Anden som gjorts mot lagrings tjänsten och förbättra prestanda för program som använder en tjänst.  

 Lagringsanalys mått är aktiverade som standard för nya lagrings konton. Du kan konfigurera mått i [Azure Portal](https://portal.azure.com/); Mer information finns i [övervaka ett lagrings konto i Azure Portal](/azure/storage/storage-monitor-storage-account). Du kan också aktivera Lagringsanalys program mässigt via REST API eller klient biblioteket. Använd åtgärderna ange tjänst egenskaper för att aktivera Lagringsanalys för varje tjänst.  

> [!NOTE]
> Lagringsanalys mått är tillgängliga för BLOB-, Queue-, Table-och File-tjänsterna.
> Lagringsanalys mått är nu klassiska mått. Microsoft rekommenderar att [lagrings mått används i Azure Monitor](storage-metrics-in-azure-monitor.md) i stället för Lagringsanalys mått.

## <a name="transaction-metrics"></a>Transaktions mått  
 En robust uppsättning data registreras i timmar eller minuter för varje lagrings tjänst och begärd API-åtgärd, inklusive ingångs-/utgångs-, tillgänglighets-, fel-och kategoriserade förfrågningar i procent. Du kan se en fullständig lista över transaktions informationen i avsnittet [Lagringsanalys Metrics tabell schema](/rest/api/storageservices/storage-analytics-metrics-table-schema) .  

 Transaktions data registreras på två nivåer – tjänste nivån och API-åtgärds nivån. På service nivå skrivs statistik som sammanfattar alla begärda API-åtgärder till en tabell enhet varje timme även om inga förfrågningar har gjorts till tjänsten. På API-åtgärds nivån skrivs statistik endast till en entitet om åtgärden begärdes inom den timmen.  

 Om du till exempel utför en **GetBlob** -åtgärd på BLOB service, kommer Lagringsanalys mått att logga begäran och inkludera den i de sammanställda data för både BLOB service och **GetBlob** -åtgärden. Men om ingen **GetBlob** -åtgärd begärs under timmen skrivs ingen entitet till *$MetricsTransactionsBlob* för den åtgärden.  

 Transaktions mått registreras för både användar förfrågningar och begär Anden som görs av Lagringsanalys. Till exempel registreras begär Anden som Lagringsanalys till Skriv loggar och tabell enheter.

## <a name="capacity-metrics"></a>Kapacitets mått  

> [!NOTE]
>  För närvarande är kapacitets måtten bara tillgängliga för Blob Service.

 Kapacitets data registreras dagligen för ett lagrings kontos Blob Service och två tabell enheter skrivs. En entitet ger statistik för användar data och den andra innehåller statistik om den `$logs` BLOB-behållare som används av Lagringsanalys. Tabellen *$MetricsCapacityBlob* innehåller följande statistik:  

- **Kapacitet**: mängden lagrings utrymme som används av lagrings kontots BLOB service, i byte.  
- **ContainerCount**: antalet BLOB-behållare i lagrings kontots BLOB service.  
- **ObjectCount**: antalet allokerade och icke allokerade block eller sid-blobar i lagrings kontots BLOB service.  

  Mer information om kapacitets mått finns i [Lagringsanalys Metrics Table schema](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Hur mått lagras  

 Alla mått data för var och en av lagrings tjänsterna lagras i tre tabeller som reserver ATS för tjänsten: en tabell för transaktionsinformation, en tabell för minut transaktions information och en annan tabell för kapacitets information. Transaktions-och minut transaktions information består av begär ande-och svars data och kapacitets information består av lagrings användnings data. Tim mått, minut mått och kapacitet för ett lagrings kontos Blob Service kan nås i tabeller som heter enligt beskrivningen i följande tabell.  

|Mått nivå|Tabell namn|Stöds för versioner|  
|-------------------|-----------------|----------------------------|  
|Tim mått, primär plats|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Versioner före 2013-08-15. Även om dessa namn fortfarande stöds, rekommenderar vi att du växlar till att använda tabellerna i listan nedan.|  
|Tim mått, primär plats|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Alla versioner. Stöd för fil tjänst mått är bara tillgängligt i version 2015-04-05 och senare.|  
|Minut mått, primär plats|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-$MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Alla versioner. Stöd för fil tjänst mått är bara tillgängligt i version 2015-04-05 och senare.|  
|Tim mått, sekundär plats|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-$MetricsHourSecondaryTransactionsQueue|Alla versioner. Geo-redundant replikering med Läs behörighet måste vara aktiverat.|  
|Minut mått, sekundär plats|-$MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-$MetricsMinuteSecondaryTransactionsQueue|Alla versioner. Geo-redundant replikering med Läs behörighet måste vara aktiverat.|  
|Kapacitet (endast Blob Service)|$MetricsCapacityBlob|Alla versioner.|  

 Tabellerna skapas automatiskt när Lagringsanalys har Aktiver ATS för lagrings tjänstens slut punkt. De nås via lagrings kontots namnrymd, till exempel: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Mått tabellerna visas inte i en List åtgärd och måste nås direkt via tabell namnet.  

## <a name="enable-metrics-using-the-azure-portal"></a>Aktivera mått med hjälp av Azure Portal
Följ dessa steg om du vill aktivera mått i [Azure Portal](https://portal.azure.com):

1. Navigera till ditt lagringskonto.
1. Välj **diagnostikinställningar (klassisk)** i **meny** fönstret.
1. Kontrol lera att **status** är inställt **på on**.
1. Välj mått för de tjänster som du vill övervaka.
1. Ange en bevarande princip för att ange hur länge mått och loggdata ska sparas.
1. Välj **Spara**.

[Azure Portal](https://portal.azure.com) kan för närvarande inte konfigurera minut mått i ditt lagrings konto. Du måste aktivera minut mått med PowerShell eller program mässigt.

## <a name="enable-storage-metrics-using-powershell"></a>Aktivera lagrings mått med PowerShell  
Du kan använda PowerShell på din lokala dator för att konfigurera lagrings mått i ditt lagrings konto med hjälp av Azure PowerShell cmdlet **Get-AzStorageServiceMetricsProperty** för att hämta de aktuella inställningarna och cmdleten  **Ange AzStorageServiceMetricsProperty** om du vill ändra de aktuella inställningarna.  

De cmdletar som styr lagrings måtten använder följande parametrar:  

* **ServiceType**, möjliga värde är **BLOB**, **kö**, **tabell**och **fil**.
* **MetricsType**, möjliga värden är **Hour** och **Minute**.  
* **MetricsLevel**, möjliga värden är:
* **Ingen**: stänger av övervakning.
* **Tjänst**: samlar in mått som ingångs-/utgångs-, tillgänglighets-, fördröjnings-och procent andelar, som sammanställs för BLOB-, kö-, tabell-och fil tjänster.
* **ServiceAndApi**: förutom tjänste måtten samlas samma uppsättning mått för varje lagrings åtgärd i Azure Storage tjänst-API: et.

Följande kommando växlar till exempel på minut mått för Blob-tjänsten i ditt lagrings konto med kvarhållningsperioden inställd på fem dagar: 

> [!NOTE]
> Det här kommandot förutsätter att du har loggat in på Azure-prenumerationen med hjälp av kommandot `Connect-AzAccount`.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Ersätt `<resource-group-name>` placeholder-värdet med namnet på din resurs grupp.
        
* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.



Följande kommando hämtar den aktuella mått nivån per timme och bevarande dagar för Blob-tjänsten på ditt standard lagrings konto:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Information om hur du konfigurerar Azure PowerShell-cmdletar så att de fungerar med din Azure-prenumeration och hur du väljer det standard lagrings konto som ska användas finns i: [så här installerar och konfigurerar du Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Aktivera lagrings mått program mässigt  
Förutom att använda Azure Portal eller Azure PowerShell-cmdletar för att kontrol lera lagrings måtten kan du också använda en av Azure Storage API: erna. Om du till exempel använder ett .NET-språk kan du använda lagrings klient biblioteket.  

Klasserna **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**och **CloudFileClient** har metoder som **SetServiceProperties** och **SetServicePropertiesAsync** som tar en  **ServiceProperties** -objekt som en parameter. Du kan använda **ServiceProperties** -objektet för att konfigurera lagrings mått. Följande C# kodfragment visar till exempel hur du ändrar mått nivån och bevarande dagar för varje timmes mått i kö:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Mer information om hur du använder ett .NET-språk för att konfigurera lagrings mått finns i [lagrings klient bibliotek för .net](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Allmän information om hur du konfigurerar lagrings mått med hjälp av REST API finns i [Aktivera och konfigurera Lagringsanalys](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Visa lagrings mått  
När du har konfigurerat Lagringsanalys mått för att övervaka ditt lagrings konto, Lagringsanalys registrerar måtten i en uppsättning välkända tabeller i ditt lagrings konto. Du kan konfigurera diagram för att Visa Tim mått i [Azure Portal](https://portal.azure.com):

1. Navigera till ditt lagrings konto i [Azure Portal](https://portal.azure.com).
1. Välj **mått (klassisk)** i **meny** bladet för den tjänst vars mått du vill visa.
1. Klicka på det diagram som du vill konfigurera.
1. På bladet **Redigera diagram** väljer du **tidsintervall**, **diagram typ**och de mått som du vill ska visas i diagrammet.

I avsnittet **övervakning (klassisk)** på lagrings kontots meny blad i Azure Portal kan du konfigurera [varnings regler](#metrics-alerts), till exempel skicka e-postaviseringar för att meddela dig när ett visst mått når ett visst värde.

Om du vill hämta måtten för långsiktig lagring eller analysera dem lokalt måste du använda ett verktyg eller skriva kod för att läsa tabellerna. Du måste ladda ned minut måtten för analys. Tabellerna visas inte om du visar alla tabeller i ditt lagrings konto, men du kan komma åt dem direkt efter namn. Många verktyg för lagrings surfning är medvetna om dessa tabeller och gör att du kan visa dem direkt (se [Azure Storage klient verktyg](/azure/storage/storage-explorers) för en lista över tillgängliga verktyg).

||||  
|-|-|-|  
|**Mått**|**Tabell namn**|**Anteckningar**|  
|Tim mått|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|I tidigare versioner än 2013-08-15 kallades dessa tabeller:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Mått för fil tjänsten är tillgängliga från och med version 2015-04-05.|  
|Minut mått|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Kan bara aktive ras med PowerShell eller program mässigt.<br /><br /> Mått för fil tjänsten är tillgängliga från och med version 2015-04-05.|  
|Kapacitet|$MetricsCapacityBlob|Endast Blob Service.|  

Du hittar fullständig information om scheman för dessa tabeller i [Lagringsanalys Metrics tabell schema](/rest/api/storageservices/storage-analytics-metrics-table-schema). I exempel raderna nedan visas endast en delmängd av tillgängliga kolumner, men vi illustrerar några viktiga funktioner i hur lagrings mått sparar dessa mått:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Tidsstämpel**|**TotalRequests**|**TotalBillableRequests**|**Total ingress**|**TotalEgress**|**Tillgänglighet**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|användarvänlig Vissa|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|användarvänlig QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7,8|100|  
|20140522T1100|användarvänlig QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|användarvänlig UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

I det här exemplet på en minuts mått data, använder partitionsnyckel tiden vid minut upplösning. Rad nyckeln identifierar den typ av information som lagras i raden och detta består av två informations typer, åtkomst typen och typen av begäran:  

-   Åtkomst typen är antingen **användare** eller **system**, där **användaren** refererar till alla användares begär anden till lagrings tjänsten och **systemet** refererar till begär Anden som görs av Lagringsanalys.  

-   Typen av begäran är antingen **alla** i det här fallet en sammanfattnings rad, eller så identifierar den det specifika API: t, till exempel **QueryEntity** eller **UpdateEntity**.  

Exempel data ovan visar alla poster för en enda minut (från 11:10:00), så antalet **QueryEntities** -begäranden plus antalet **QueryEntity** -begäranden plus antalet **UpdateEntity** -begäranden som är upp till sju, vilket är Totalt antal som visas på **användaren: all** -rad. På samma sätt kan du härleda den genomsnittliga svars tiden från slut punkt till slut punkt 104,4286 för **användaren: all** rad genom att beräkna ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Mått varningar
Du bör överväga att ställa in aviseringar i [Azure Portal](https://portal.azure.com) så att du automatiskt får ett meddelande om viktiga förändringar i hur dina lagrings tjänster fungerar. Om du använder ett verktyg för lagrings Utforskaren för att hämta dessa mått data i ett avgränsat format kan du använda Microsoft Excel för att analysera data. Se [Azure Storage klient verktyg](/azure/storage/storage-explorers) för en lista över tillgängliga verktyg för lagrings Utforskaren. Du kan konfigurera aviseringar på bladet **avisering (klassisk)** som är tillgängligt under **övervakning (klassisk)** på Meny bladet lagrings konto.

> [!IMPORTANT]
> Det kan finnas en fördröjning mellan en lagrings händelse och när motsvarande tim-eller minut mått data registreras. Om det gäller minut mått kan flera minuters data skrivas samtidigt. Detta kan leda till transaktioner från tidigare minuter som sammanställs i transaktionen för den aktuella minuten. När detta inträffar kanske aviserings tjänsten inte har alla tillgängliga mått data för det konfigurerade aviserings intervallet, vilket kan leda till att aviseringar uppstår oväntade.
>

## <a name="accessing-metrics-data-programmatically"></a>Åtkomst till mått data program mässigt  
I följande lista visas exempel C# kod som har åtkomst till minut måtten för ett antal minuter och visar resultatet i ett konsol fönster. Kod exemplet använder Azure Storage klient bibliotek version 4. x eller senare, vilket omfattar klassen **CloudAnalyticsClient** som fören klar åtkomsten till mått tabellerna i Storage.  

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

## <a name="billing-on-storage-metrics"></a>Fakturering av lagrings mått  
Skriv förfrågningar för att skapa tabell enheter för mått debiteras enligt de standard priser som gäller för alla Azure Storage åtgärder.  

Läs-och borttagnings begär Anden för Mät data av en klient faktureras också enligt standardpriser. Om du har konfigurerat en princip för data bevarande debiteras du inte när Azure Storage tar bort gamla mått data. Men om du tar bort Analytics-data debiteras ditt konto för borttagnings åtgärderna.  

Kapaciteten som används av mått tabellerna är också fakturerbar. Du kan använda följande för att beräkna hur mycket kapacitet som används för att lagra mått data:  

-   Om varje timme en tjänst använder varje API i varje tjänst, lagras ungefär 148KB data varje timma i tabellerna med mått transaktioner om du har aktiverat både Sammanfattning av service-och API-nivå.  
-   Om du inom varje timme använder en tjänst varje API i tjänsten, lagras ungefär 12KB data varje timma i tabellerna med mått transaktioner om du har aktiverat bara sammanfattning på tjänst nivå.  
-   Kapacitets tabellen för blobbar har två rader tillagda varje dag, förutsatt att du har valt att logga in. Detta innebär att storleken på den här tabellen ökar med cirka 300 byte varje dag.

## <a name="next-steps"></a>Nästa steg
* [Övervaka ett lagrings konto](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schema för Lagringsanalys mått tabellen](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Lagringsanalys loggning](storage-analytics-logging.md)
