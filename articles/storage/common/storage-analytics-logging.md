---
title: Loggning med Azure-lagringsanalys
description: Läs om hur du loggar information om begäranden som görs mot Azure Storage.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5b94a97f1286e1273300014e4eef140be412436b
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637183"
---
# <a name="azure-storage-analytics-logging"></a>Loggning med Azure-lagringsanalys

Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst. Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Förfrågningar loggas på bästa sätt.

 Lagringsanalysloggning är inte aktiverat som standard för ditt lagringskonto. Du kan aktivera den i [Azure-portalen](https://portal.azure.com/). Mer information finns [i Övervaka ett lagringskonto i Azure-portalen](/azure/storage/storage-monitor-storage-account). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Använd åtgärderna [Hämta Blob-tjänstegenskaper,](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API) [Hämta egenskaper för kötjänst](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)och Hämta egenskaper för [table service](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) för att aktivera Lagringsanalys för varje tjänst.

 Loggtransaktioner skapas endast om det finns begäranden som görs mot tjänstens slutpunkt. Om till exempel ett lagringskonto har aktivitet i blob-slutpunkten men inte i dess tabell- eller köslutpunkter skapas endast loggar som hör till Blob-tjänsten.

> [!NOTE]
>  Lagringsanalysloggning är för närvarande endast tillgängligt för blob-, kö- och tabelltjänsterna. Premium Storage-konto stöds dock inte.

## <a name="requests-logged-in-logging"></a>Begäranden som loggas inloggad
### <a name="logging-authenticated-requests"></a>Logga autentiserade begäranden

 Följande typer av autentiserade begäranden loggas:

- Lyckade begäranden
- Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel
- Begäranden med en signatur för delad åtkomst (SAS), inklusive misslyckade och lyckade begäranden
- Begäranden om analysdata

  Begäranden som görs av Storage Analytics själv, till exempel att skapa logg eller ta bort dem, loggas inte. En fullständig lista över de loggade data dokumenteras i avsnitten [Lagringsanalysloggade operationer och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [lagringsanalysloggformat.](/rest/api/storageservices/storage-analytics-log-format)

### <a name="logging-anonymous-requests"></a>Logga anonyma förfrågningar

 Följande typer av anonyma begäranden loggas:

- Lyckade begäranden
- Serverfel
- Timeout-fel för både klient och server
- Misslyckade GET-begäranden med felkoden 304 (har inte ändrats)

  Alla andra misslyckade anonyma begäranden loggas inte. En fullständig lista över de loggade data dokumenteras i avsnitten [Lagringsanalysloggade operationer och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [lagringsanalysloggformat.](/rest/api/storageservices/storage-analytics-log-format)

## <a name="how-logs-are-stored"></a>Så här lagras loggar

Alla loggar lagras i blockblobar i `$logs`en behållare med namnet , som skapas automatiskt när Storage Analytics är aktiverat för ett lagringskonto. Behållaren `$logs` finns i blob-namnområdet för lagringskontot, till exempel: `http://<accountname>.blob.core.windows.net/$logs`. Den här behållaren kan inte tas bort när Storage Analytics har aktiverats, även om innehållet kan tas bort. Om du använder ditt lagringsbläddringsverktyg för att navigera direkt till behållaren visas alla blobbar som innehåller loggningsdata.

> [!NOTE]
>  Behållaren `$logs` visas inte när en behållarlistning utförs, till exempel åtgärden Lista behållare. Den måste nås direkt. Du kan till exempel använda åtgärden Lista Blobbar för `$logs` att komma åt blobbar i behållaren.

När begäranden loggas kommer Storage Analytics att överföra mellanliggande resultat som block. Med jämna mellanrum kommer Storage Analytics att genomföra dessa block och göra dem tillgängliga som en blob. Det kan ta upp till en timme innan loggdata visas i blobbar i **$logs** behållaren eftersom frekvensen som lagringstjänsten tömmer loggskrivare. Dubblettposter kan finnas för loggar som skapas under samma timme. Du kan avgöra om en post är en dubblett genom att kontrollera **numret RequestId** och **Operation.**

Om du har en stor mängd loggdata med flera filer för varje timme kan du använda blobmetadata för att avgöra vilka data loggen innehåller genom att undersöka blobmetadatafälten. Detta är också användbart eftersom det ibland kan finnas en fördröjning medan data skrivs till loggfilerna: blobmetadata ger en mer exakt indikation på blob-innehållet än blobnamnet.

Med de flesta verktyg för lagringsbläddring kan du visa metadata för blobbar. Du kan också läsa den här informationen med PowerShell eller programmässigt. Följande PowerShell-kodavsnitt är ett exempel på filtrering av listan över loggblobar efter namn för att ange en tid och metadata för att identifiera bara de loggar som innehåller **skrivåtgärder.**  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Information om hur du listar blobbar programmässigt finns i [Räkna upp Blob-resurser](https://msdn.microsoft.com/library/azure/hh452233.aspx) och [inställning och hämta egenskaper och metadata för Blob-resurser](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Logga namngivningskonventioner

 Varje logg kommer att skrivas i följande format:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 I följande tabell beskrivs varje attribut i loggnamnet:

|Attribut|Beskrivning|
|---------------|-----------------|
|`<service-name>`|Namnet på lagringstjänsten. Till `blob`exempel: `table`, eller`queue`|
|`YYYY`|Det fyrsiffriga året för loggen. Exempel: `2011`|
|`MM`|Den tvåsiffriga månaden för loggen. Exempel: `07`|
|`DD`|Den tvåsiffriga dagen för loggen. Exempel: `31`|
|`hh`|Den tvåsiffriga timme som anger starttimmen för loggarna, i 24-timmars UTC-format. Exempel: `18`|
|`mm`|Det tvåsiffriga tal som anger loggloggens startminut. **Anm.:**  Det här värdet stöds inte i den aktuella versionen av `00`Storage Analytics, och dess värde kommer alltid att vara .|
|`<counter>`|En nollbaserad räknare med sex siffror som anger antalet loggblobar som genereras för lagringstjänsten under en timme tidsperiod. Den här `000000`räknaren börjar vid . Exempel: `000001`|

 Följande är ett fullständigt exempel loggnamn som kombinerar ovanstående exempel:

 `blob/2011/07/31/1800/000001.log`

 Följande är ett exempel URI som kan användas för att komma åt ovanstående logg:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 När en lagringsbegäran loggas korrelerar det resulterande loggnamnet till den timme då den begärda åtgärden slutfördes. Om en GetBlob-begäran till exempel slutfördes klockan 18:30 den 2011-07-31, skrivs loggen med följande prefix:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Logga metadata

 Alla loggblobar lagras med metadata som kan användas för att identifiera vilka loggningsdata bloben innehåller. I följande tabell beskrivs varje metadataattribut:

|Attribut|Beskrivning|
|---------------|-----------------|
|`LogType`|Beskriver om loggen innehåller information om att läsa, skriva eller ta bort åtgärder. Det här värdet kan innehålla en typ eller en kombination av alla tre, avgränsade med kommatecken.<br /><br /> Exempel 1:`write`<br /><br /> Exempel 2:`read,write`<br /><br /> Exempel 3:`read,write,delete`|
|`StartTime`|Den tidigaste tiden för en post i `YYYY-MM-DDThh:mm:ssZ`loggen, i form av . Exempel: `2011-07-31T18:21:46Z`|
|`EndTime`|Den senaste tiden för en post i `YYYY-MM-DDThh:mm:ssZ`loggen, i form av . Exempel: `2011-07-31T18:22:09Z`|
|`LogVersion`|Den version av loggformatet.|

 I följande lista visas fullständiga exempelmetadata med hjälp av ovanstående exempel:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Aktivera lagringsloggning

Du kan aktivera lagringsloggning med Azure-portal, PowerShell och Storage SDK:er.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Aktivera lagringsloggning med Azure-portalen  

I Azure-portalen använder du **bladet Diagnostikinställningar (klassiska)** för att styra lagringsloggning, som är tillgängligt från avsnittet **Övervakning (klassisk)** i ett lagringskontos **menyblad**.

Du kan ange vilka lagringstjänster som du vill logga och kvarhållningsperioden (i dagar) för de loggade data.  

### <a name="enable-storage-logging-using-powershell"></a>Aktivera lagringsloggning med PowerShell  

 Du kan använda PowerShell på din lokala dator för att konfigurera lagringsloggning i ditt lagringskonto med hjälp av Azure PowerShell cmdlet **Get-AzureStorageServiceLoggingProperty** för att hämta de aktuella inställningarna och cmdlet **Set-AzureStorageServiceLoggingProperty** för att ändra de aktuella inställningarna.  

 De cmdlets som styr lagringsloggning använder en **LoggingOperations-parameter** som är en sträng som innehåller en kommaavgränsad lista över begärandetyper att logga. De tre möjliga typerna av begäranden **läss,** **skrivs**och **tas bort**. Om du vill inaktivera loggning använder du värdet **ingen** för parametern **LoggingOperations.**  

 Följande kommando växlar på loggning för läsning, skrivning och borttagning av begäranden i kötjänsten i ditt standardlagringskonto med kvarhållning inställd på fem dagar:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Följande kommando stänger av loggning för tabelltjänsten i ditt standardlagringskonto:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Information om hur du konfigurerar Azure PowerShell-cmdletar för att fungera med din Azure-prenumeration och hur du väljer det standardlagringskonto som ska användas finns i: [Installera och konfigurera Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Aktivera lagringsloggning programmässigt  

 Förutom att använda Azure-portalen eller Azure PowerShell-cmdlets för att styra lagringsloggning kan du också använda en av Azure Storage API:er. Om du till exempel använder ett .NET-språk kan du använda storage-klientbiblioteket.  

 Klasserna **CloudBlobClient,** **CloudQueueClient**och **CloudTableClient** har alla metoder som **SetServiceProperties** och **SetServicePropertiesAsync** som tar ett **ServiceProperties-objekt** som en parameter. Du kan använda **ServiceProperties-objektet** för att konfigurera lagringsloggning. Följande C#-kodavsnitt visar till exempel hur du ändrar vad som loggas och kvarhållningsperioden för köloggning:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Mer information om hur du använder ett .NET-språk för att konfigurera lagringsloggning finns i [Referens för lagringsklientbibliotek](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Allmän information om hur du konfigurerar lagringsloggning med REST API finns i [Aktivera och konfigurera Lagringsanalys](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Hämta loggdata för lagringsloggning

 Om du vill visa och analysera loggdata bör du hämta blobbar som innehåller loggdata som du är intresserad av till en lokal dator. Med många verktyg för lagringsbläddring kan du hämta blobbar från ditt lagringskonto. Du kan också använda Azure Storage-teamet som kommandoraden Azure Copy Tool [AzCopy](storage-use-azcopy-v10.md) för att hämta dina loggdata.  
 
>[!NOTE]
> Behållaren `$logs` är inte integrerad med Event Grid, så du får inga aviseringar när loggfiler skrivs. 

 Så här kontrollerar du att du hämtar loggdata som du är intresserad av och för att undvika att du hämtar samma loggdata mer än en gång:  

-   Använd datum- och tidsnamngivningskonventionen för blobbar som innehåller loggdata för att spåra vilka blobbar som du redan har hämtat för analys för att undvika att hämta samma data igen mer än en gång.  

-   Använd metadata på blobbar som innehåller loggdata för att identifiera den specifika period för vilken bloben innehåller loggdata för att identifiera exakt den blob som du behöver hämta.  

För att komma igång med AzCopy, se [Komma igång med AzCopy](storage-use-azcopy-v10.md) 

I följande exempel visas hur du kan hämta loggdata för kötjänsten för timmarna från 09.00, 10.00 och 11.00 den 20 maj 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Mer information om hur du hämtar specifika filer finns i [Hämta specifika filer](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files).

När du har hämtat loggdata kan du visa loggposterna i filerna. Dessa loggfiler använder ett avgränsat textformat som många loggläsningsverktyg kan tolka, inklusive Microsoft Message Analyzer (mer information finns i guiden [Övervakning, diagnos och felsökning av Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Olika verktyg har olika möjligheter att formatera, filtrera, sortera, annonssökning innehållet i dina loggfiler. Mer information om loggfilsformatet och innehållet i loggfilen för lagring finns i [Lagringsanalysloggformat](/rest/api/storageservices/storage-analytics-log-format) och [lagringsanalys loggade åtgärder och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Nästa steg

* [Loggformat för lagringsanalys](/rest/api/storageservices/storage-analytics-log-format)
* [Loggade åtgärder och statusmeddelanden för lagringsanalys](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Mätvärden för lagringsanalys (klassisk)](storage-analytics-metrics.md)
