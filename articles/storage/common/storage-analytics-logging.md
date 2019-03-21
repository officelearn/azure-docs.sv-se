---
title: Azure Storage Analytics-loggning
description: Lär dig mer om att logga information om begäranden som görs mot Azure Storage.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: ab235c67e3a0e60999a0348d03a6e938944f7030
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260202"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage analytics-loggning

Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst. Den här informationen kan användas för att övervaka enskilda begäranden och diagnostisera problem med en lagringstjänst. Förfrågningar loggas på basis av bästa prestanda.

 Storage Analytics loggning är inte aktiverat som standard för ditt lagringskonto. Du kan aktivera den i den [Azure-portalen](https://portal.azure.com/); mer information finns i [övervaka ett lagringskonto i Azure-portalen](/azure/storage/storage-monitor-storage-account). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Använd den [hämta egenskaper för Blob Service](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [hämta egenskaper för kötjänst](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties), och [hämta tabell tjänstegenskaper](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) åtgärder för att aktivera Storage Analytics för varje tjänst.

 Loggposter skapas endast om det finns förfrågningar mot tjänsteslutpunkt. Till exempel skapas ett storage-konto har aktivitet i dess Blob-slutpunkt, men inte i dess tabell- eller slutpunkter, endast loggar som hör till Blob-tjänsten.

> [!NOTE]
>  Loggningen i Storage Analytics finns för närvarande endast för Blob, Queue och Table-tjänster. Dock stöds inte premium storage-konto.

## <a name="requests-logged-in-logging"></a>Begäranden som har loggat in loggning

### <a name="logging-authenticated-requests"></a>Autentiserade loggningsbegäranden

 Följande typer av autentiserade begäranden loggas:

- Lyckade begäranden
- Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel
- Begäranden som använder en signatur för delad åtkomst (SAS) eller OAuth, inklusive misslyckade och lyckade förfrågningar
- Begäranden till analytics-data

  Begäranden som görs av Storage Analytics, till exempel log skapas eller tas bort, loggas inte. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [Storage Analytics loggformat](/rest/api/storageservices/storage-analytics-log-format) ämnen.

### <a name="logging-anonymous-requests"></a>Logga anonyma förfrågningar

 Följande typer av anonyma förfrågningar loggas:

- Lyckade begäranden
- Serverfel
- Timeout-fel för både klient och server
- Misslyckade GET-begäranden med felkoden 304 (har inte ändrats)

  Alla övriga misslyckade anonyma förfrågningar loggas inte. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [Storage Analytics loggformat](/rest/api/storageservices/storage-analytics-log-format) ämnen.

## <a name="how-logs-are-stored"></a>Hur loggar lagras

Alla loggar lagras i blockblob-objekt i en behållare med namnet `$logs`, som skapas automatiskt när Storage Analytics är aktiverat för ett lagringskonto. Den `$logs` behållare finns i blob-namnområdet för storage-konto, till exempel: `http://<accountname>.blob.core.windows.net/$logs`. Den här behållaren kan inte tas bort när Storage Analytics har aktiverats, även om dess innehåll kan tas bort. Om du använder ditt surfa på storage-verktyg för att navigera till behållaren direkt, visas alla blobar som innehåller dina loggningsdata.

> [!NOTE]
>  Den `$logs` behållaren visas inte när en behållare med åtgärden utförs, till exempel listan över behållare igen. Den måste kunna nås direkt. Du kan till exempel använda åtgärden listar Blobbar komma åt i det `$logs` behållare.

Eftersom förfrågningar loggas, ska Storage Analytics överföra mellanresultat som block. Lagringsanalys kommer med jämna mellanrum, genomför de här block och göra dem tillgängliga som en blob. Det kan ta upp till en timme efter loggdata som visas i blobarna i den **$logs** behållare eftersom den frekvens som lagringstjänsten cachetömningar log-skrivare. Dubbla poster kan finnas för loggar som skapas i en och samma timme. Du kan avgöra om en post är en dubblett genom att markera den **RequestId** och **åtgärden** tal.

Om du har ett stort antal loggdata med flera filer för varje timme, kan du använda blobmetadata att avgöra vilka data som loggen innehåller genom att undersöka fält för blob-metadata. Detta är också användbart eftersom det kan ibland uppstå en fördröjning när data skrivs till loggfilerna: blobmetadata ger en exaktare indikation på blob-innehåll än blobnamnet.

De flesta bläddrat storage-verktyg kan du visa metadata för blobar; Du kan också läsa den här informationen med hjälp av PowerShell eller via programmering. Följande PowerShell-kodavsnitt är ett exempel på att filtrera listan över loggblobarna genom namn för att ange en tid och metadata för att identifiera de loggar som innehåller **skriva** åtgärder.  

 ```  
 Get-AzureStorageBlob -Container '$logs' |  
 where {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 foreach {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Läs om hur lista blobbar programmässigt [uppräkning av Blob-resurser](http://msdn.microsoft.com/library/azure/hh452233.aspx) och [inställning och hämtning av egenskaper och Metadata för Blob-resurser](http://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Logga namngivningskonventioner

 Varje logg skrivs i följande format:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 I följande tabell beskrivs alla attribut i namnet på loggen:

|Attribut|Beskrivning|
|---------------|-----------------|
|`<service-name>`|Namnet på storage-tjänsten. Till exempel: `blob`, `table`, eller `queue`|
|`YYYY`|Fyrsiffrigt år för loggen. Exempel: `2011`|
|`MM`|Månaden med två siffror för loggen. Exempel: `07`|
|`DD`|Dagen med två siffror i loggen. Exempel: `31`|
|`hh`|Två siffror timmen som anger den första timmen för loggarna, 24-timmarsformat UTC format. Exempel: `18`|
|`mm`|De två siffror som anger från minut för loggarna. **Obs!**  Det här värdet stöds inte i den aktuella versionen av Storage Analytics och dess värde alltid kommer att `00`.|
|`<counter>`|Ett nollbaserat räknare med sex siffror som anger antalet loggblobarna som genererats för storage-tjänsten i en timme lång tid. Den här räknaren börjar vid `000000`. Exempel: `000001`|

 Följande är ett fullständigt exempel loggnamn som kombinerar ovanstående exempel:

 `blob/2011/07/31/1800/000001.log`

 Följande är ett exempel på URI: N som kan användas för att komma åt ovan loggen:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 När en begäran om storage loggas, motsvarar det resulterande Loggnamnet timme när den begärda åtgärden har slutförts. Om en GetBlob begäran slutfördes vid 18:30:00 den 7/31/2011, skulle loggen skrivas med följande prefix: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Log-metadata

 Alla loggblobarna lagras med metadata som kan användas för att identifiera vilka loggning blobben innehåller. I följande tabell beskrivs alla metadataattribut:

|Attribut|Beskrivning|
|---------------|-----------------|
|`LogType`|Beskriver om loggen innehåller information om för att läsa, skriva eller ta bort åtgärder. Det här värdet kan innehålla en typ eller en kombination av alla tre, avgränsade med kommatecken.<br /><br /> Exempel 1: `write`<br /><br /> Exempel 2: `read,write`<br /><br /> Exempel 3: `read,write,delete`|
|`StartTime`|Den tidigaste tidpunkten för en post i loggen, i form av `YYYY-MM-DDThh:mm:ssZ`. Exempel: `2011-07-31T18:21:46Z`|
|`EndTime`|Senaste tid för en post i loggen, i form av `YYYY-MM-DDThh:mm:ssZ`. Exempel: `2011-07-31T18:22:09Z`|
|`LogVersion`|Versionen av loggformatet.|

 Följande lista visar hela exemplet metadata med hjälp av ovanstående exempel:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Aktivera loggning för lagring

Du kan aktivera loggningen i Storage med Azure portal, PowerShell och Storage SDK: er.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Aktivera loggning för lagring med hjälp av Azure portal  

I Azure-portalen använder den **diagnostikinställningar (klassisk)** bladet för att styra Storage loggning, kan nås från den **övervakning (klassisk)** delen av ett lagringskonto **Appmenyns blad** .

Du kan ange de storage-tjänster som du vill logga in och kvarhållningsperioden (i dagar) för data som loggats.  

### <a name="enable-storage-logging-using-powershell"></a>Aktivera loggning för lagring med hjälp av PowerShell  

 Du kan använda PowerShell på den lokala datorn för att konfigurera Storage loggning i ditt storage-konto med hjälp av Azure PowerShell-cmdleten **Get-AzureStorageServiceLoggingProperty** att hämta de aktuella inställningarna och cmdleten  **Set-AzureStorageServiceLoggingProperty** att ändra de aktuella inställningarna.  

 Cmdletar som styr loggning av Storage använder en **LoggingOperations** parameter som är en sträng som innehåller en kommaavgränsad lista över typer av begäranden att logga in. De tre typerna av begäran är **läsa**, **skriva**, och **ta bort**. Om du vill inaktivera loggning, använder du värdet **ingen** för den **LoggingOperations** parametern.  

 Följande kommando växlar loggning för läsning, skriva och ta bort begäranden i kö-tjänst i din standardkontot för lagring med kvarhållning inställd på fem dagar:  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Queue   
-LoggingOperations read,write,delete -RetentionDays 5  
```  

 Följande kommando växlar stänga av loggning för tabelltjänsten i standardkontot för lagring:  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Table   
-LoggingOperations none  
```  

 Information om hur du konfigurerar finns Azure PowerShell-cmdletar för att arbeta med din Azure-prenumeration och hur du väljer standardkontot för lagring att använda, i: [Hur du installerar och konfigurerar du Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Aktivera Storage logging programmässigt  
 Förutom att använda Azure portal eller Azure PowerShell-cmdletar för att styra Storage loggning kan använda du också en av API: erna för Azure Storage. Du kan till exempel använda Lagringsklientbiblioteket om du använder ett .NET-språk.  

 Klasserna **CloudBlobClient**, **CloudQueueClient**, och **CloudTableClient** alla har metoder som **SetServiceProperties** och **SetServicePropertiesAsync** som tar en **ServiceProperties** objektet som en parameter. Du kan använda den **ServiceProperties** objekt för att konfigurera Storage loggning. Till exempel följande C# kodfragment visar hur du ändrar vad loggas och kvarhållningsperioden för kön loggning:  

```  
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Mer information om hur du använder ett .NET-språk för att konfigurera Storage loggning finns i [Storage-Klientbiblioteksreferens](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Allmän information om hur du konfigurerar Storage loggning med hjälp av REST-API finns i [aktivera och konfigurera Lagringsanalys](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Ladda ned loggning loggdata för lagring

 Om du vill visa och analysera loggdata, bör du hämta BLOB-objekt som innehåller loggdata som du är intresserad av i till en lokal dator. Många surfa på storage-verktyg kan du ladda ned blobbar från lagringskontot; Du kan också använda Azure Storage-teamet som tillhandahålls av Azure kopiera kommandoradsverktyget (**AzCopy**) att hämta dina loggdata.  

 Kontrollera att du laddar ned loggdata som du är intresserad av och undvika att överföra samma loggdata mer än en gång:  

-   Använd datumet och tid namngivningskonvention för BLOB-objekt som innehåller loggdata att spåra vilka objekt du har redan laddats ned för att undvika att överföra nytt samma data flera gånger.  

-   Använda metadata för blobbar som innehåller loggdata för att identifiera den specifika perioden som innehåller blobben loggdata för att identifiera den exakta blob som du behöver hämta.  

> [!NOTE]
>  AzCopy är en del av Azure SDK, men du kan alltid hämta den senaste versionen från [ https://aka.ms/AzCopy ](https://aka.ms/AzCopy). AzCopy installeras som standard i mappen **C:\Program Files (x86) \Microsoft SDKs\Windows Azure\AzCopy**, och du bör lägga till den här mappen till sökvägen innan du försöker köra verktyget i en kommandotolk eller PowerShell-fönster.  

 I följande exempel visas hur du kan hämta loggdata för Kötjänsten för de timmar som börjar vid 09 kl 10 AM och 11 kl 20 maj 2014. Den **/S** parametern orsakar AzCopy för att skapa en lokal mapp-struktur som baseras på datum och tider i loggfilsnamn; den **/V** parametern orsakar AzCopy för att producera utförliga utdata; den **/Y** parametern orsakar AzCopy för att skriva över eventuella lokala filer. Ersätt **< yourstorageaccount\>**  med namnet på ditt lagringskonto och Ersätt **< yourstoragekey\>**  med din lagringskontonyckel.  

```  
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy har också vissa användbara parametrar som styr hur anges tid för senaste ändring på de filer som hämtas och om den ska försöka ladda ned filer som är äldre eller nyare än några filer som redan finns på den lokala datorn. Du kan också köra den i omstartsläge. Visa hjälp för fullständig information genom att köra den **AzCopy /?** kommandot.  

 Ett exempel på hur du laddar ned loggdata programmässigt finns i bloggposten [loggning för Windows Azure Storage: Använda loggar för att spåra lagringsbegäranden](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) och Sök efter ordet ”DumpLogs” på sidan.  

 När du har hämtat dina loggdata, ser du vilka loggposter i filerna. Dessa loggfiler använda avgränsat textformat så många läser verktyg kan parsa, inklusive Microsoft Message Analyzer (Mer information finns i guiden [övervakning, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Olika verktyg har olika funktioner för formatering, filtrering, sortering, ad söker innehållet i loggfilerna. Läs mer om Storage Logging loggfilsformat och innehåll, [Storage Analytics loggformat](/rest/api/storageservices/storage-analytics-log-format) och [Storage Analytics loggade åtgärder och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Nästa steg
* [Loggformatet för Storage Analytics](/rest/api/storageservices/storage-analytics-log-format)
* [Lagringsanalys loggade åtgärder och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Mätvärden i Storage Analytics (klassisk)](storage-analytics-metrics.md)
