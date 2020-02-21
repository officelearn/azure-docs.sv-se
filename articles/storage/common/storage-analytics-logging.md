---
title: Azure-lagringsanalys loggning
description: Lär dig hur du loggar information om begär Anden som görs mot Azure Storage.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 25c047dc9b2ce08ca39e69c6f106e41c5d9bd0dc
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484901"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage Analytics-loggning

Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst. Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Begär Anden loggas med bästa möjliga ansträngning.

 Lagringsanalysloggning är inte aktiverat som standard för ditt lagringskonto. Du kan aktivera det i [Azure Portal](https://portal.azure.com/); Mer information finns i [övervaka ett lagrings konto i Azure Portal](/azure/storage/storage-monitor-storage-account). Du kan också aktivera Lagringsanalys program mässigt via REST API eller klient biblioteket. Använd egenskaperna [Hämta BLOB service](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [Hämta egenskaper för Queue Service](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)och [Hämta tabell tjänst egenskaper](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) för att aktivera Lagringsanalys för varje tjänst.

 Logg poster skapas endast om det finns begär Anden som görs mot tjänst slut punkten. Om ett lagrings konto till exempel har aktivitet i dess BLOB-slutpunkt men inte i dess tabell-eller Queue-slutpunkter, skapas bara loggar som rör Blob Service.

> [!NOTE]
>  Lagringsanalysloggning är för närvarande endast tillgängligt för blob-, kö- och tabelltjänsterna. Premium Storage-konto stöds dock inte.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="requests-logged-in-logging"></a>Begär Anden som loggats i loggning
### <a name="logging-authenticated-requests"></a>Logga autentiserade begär Anden

 Följande typer av autentiserade begär Anden loggas:

- Lyckade förfrågningar
- Misslyckade förfrågningar, inklusive timeout, begränsning, nätverk, auktorisering och andra fel
- Begär Anden som använder en signatur för delad åtkomst (SAS) eller OAuth, inklusive misslyckade och lyckade förfrågningar
- Begär Anden för analys av data

  Begär Anden som görs av Lagringsanalys, till exempel när loggen skapas eller tas bort loggas inte. En fullständig lista över de loggade data som dokumenteras finns i avsnitten [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [Lagringsanalys logg format](/rest/api/storageservices/storage-analytics-log-format) .

### <a name="logging-anonymous-requests"></a>Logga anonyma begär Anden

 Följande typer av anonyma begär Anden loggas:

- Lyckade förfrågningar
- Server fel
- Timeout-fel för både klienten och servern
- Misslyckade GET-begäranden med felkod 304 (inte ändrad)

  Alla andra misslyckade anonyma begär Anden loggas inte. En fullständig lista över de loggade data som dokumenteras finns i avsnitten [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [Lagringsanalys logg format](/rest/api/storageservices/storage-analytics-log-format) .

## <a name="how-logs-are-stored"></a>Så här lagras loggar

Alla loggar lagras i block-blobar i en behållare med namnet `$logs`, som skapas automatiskt när Lagringsanalys har Aktiver ATS för ett lagrings konto. `$logs` containern finns i BLOB-namnrymden för lagrings kontot, till exempel: `http://<accountname>.blob.core.windows.net/$logs`. Det går inte att ta bort den här behållaren när Lagringsanalys har Aktiver ATS, men innehållet kan tas bort. Om du använder ditt sökverktyg för att navigera till behållaren direkt visas alla blobar som innehåller dina loggnings data.

> [!NOTE]
>  `$logs` container visas inte när en åtgärd för container listan utförs, till exempel list containers-åtgärden. Det måste nås direkt. Du kan till exempel använda List-blobar-åtgärden för att få åtkomst till Blobbarna i `$logs` containern.

När förfrågningar loggas kommer Lagringsanalys att överföra mellanliggande resultat som block. Med jämna mellanrum kommer Lagringsanalys att bekräfta dessa block och göra dem tillgängliga som en blob. Det kan ta upp till en timme innan loggdata visas i blobarna i **$logs** container eftersom lagrings tjänsten tömmer logg skribenterna. Det kan finnas dubbla poster för loggar som skapats i samma timme. Du kan avgöra om en post är en dubblett genom att kontrol lera **RequestId** och **Åtgärds** nummer.

Om du har en stor mängd loggdata med flera filer per timme kan du använda BLOB-metadata för att avgöra vilka data som loggen innehåller genom att undersöka fälten för BLOB-metadata. Detta är också användbart eftersom det ibland kan vara en fördröjning när data skrivs till loggfilerna: BLOB-metadata ger en mer korrekt indikation av BLOB-innehållet än BLOB-namnet.

Med de flesta verktyg för lagrings surfning kan du visa metadata för blobbar. Du kan också läsa informationen med hjälp av PowerShell eller program mässigt. Följande PowerShell-kodfragment är ett exempel på hur du filtrerar listan över logg-blobar efter namn för att ange en tid, och efter metadata för att identifiera enbart de loggar som innehåller **Skriv** åtgärder.  

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

Information om hur du registrerar blobbar program mässigt finns i [räkna upp BLOB-resurser](https://msdn.microsoft.com/library/azure/hh452233.aspx) och [Ange och hämta egenskaper och metadata för BLOB-resurser](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Logg namngivnings konventioner

 Varje logg skrivs i följande format:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 I följande tabell beskrivs varje attribut i logg namnet:

|Attribut|Beskrivning|
|---------------|-----------------|
|`<service-name>`|Namnet på lagrings tjänsten. Exempel: `blob`, `table`eller `queue`|
|`YYYY`|Årtalet med fyra siffror för loggen. Exempel: `2011`|
|`MM`|Den två siffrorna i månaden för loggen. Exempel: `07`|
|`DD`|Den två siffriga dagen för loggen. Exempel: `31`|
|`hh`|Den två siffriga timmen som anger start timmen för loggarna, i UTC-format i 24 timmar. Exempel: `18`|
|`mm`|Det två siffer numret som anger start minuten för loggarna. **Obs:**  Det här värdet stöds inte i den aktuella versionen av Lagringsanalys och värdet kommer alltid att `00`.|
|`<counter>`|En noll-baserad räknare med sex siffror som anger antalet logg-blobar som har genererats för lagrings tjänsten under en tids period. Den här räknaren startar på `000000`. Exempel: `000001`|

 Följande är ett fullständigt exempel på logg namn som kombinerar exemplen ovan:

 `blob/2011/07/31/1800/000001.log`

 Följande är en exempel-URI som kan användas för att komma åt loggen ovan:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 När en lagrings förfrågan loggas motsvarar logg namnet i timmen när den begärda åtgärden slutfördes. Om en GetBlob-begäran exempelvis slutfördes 6:17.30 på 7/31/2011, skrivs loggen med följande prefix: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Logga metadata

 Alla log-blobbar lagras med metadata som kan användas för att identifiera vilka loggnings data som bloben innehåller. I följande tabell beskrivs varje attribut för metadata:

|Attribut|Beskrivning|
|---------------|-----------------|
|`LogType`|Beskriver om loggen innehåller information som rör Läs-, Skriv-eller borttagnings åtgärder. Det här värdet kan innehålla en typ eller en kombination av alla tre, avgränsade med kommatecken.<br /><br /> Exempel 1: `write`<br /><br /> Exempel 2: `read,write`<br /><br /> Exempel 3: `read,write,delete`|
|`StartTime`|Den tidigaste tiden för en post i loggen i form av `YYYY-MM-DDThh:mm:ssZ`. Exempel: `2011-07-31T18:21:46Z`|
|`EndTime`|Den senaste tiden för en post i loggen i form av `YYYY-MM-DDThh:mm:ssZ`. Exempel: `2011-07-31T18:22:09Z`|
|`LogVersion`|Versionen av logg formatet.|

 I följande lista visas kompletta exempel-metadata med hjälp av ovanstående exempel:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Aktivera lagrings loggning

Du kan aktivera lagrings loggning med Azure Portal-, PowerShell-och lagrings-SDK: er.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Aktivera lagrings loggning med hjälp av Azure Portal  

I Azure Portal använder du bladet **diagnostikinställningar (klassisk)** för att kontrol lera lagrings loggning, som är tillgängligt från avsnittet **övervakning (klassisk)** i **meny bladet**för ett lagrings konto.

Du kan ange de lagrings tjänster som du vill logga och kvarhållningsperioden (i dagar) för de loggade data.  

### <a name="enable-storage-logging-using-powershell"></a>Aktivera lagrings loggning med PowerShell  

 Du kan använda PowerShell på din lokala dator för att konfigurera lagrings loggning i ditt lagrings konto med hjälp av Azure PowerShell cmdlet **Get-AzureStorageServiceLoggingProperty** för att hämta de aktuella inställningarna och cmdleten **set-AzureStorageServiceLoggingProperty** för att ändra de aktuella inställningarna.  

 De cmdletar som styr lagrings loggningen använder en **LoggingOperations** -parameter som är en sträng som innehåller en kommaavgränsad lista över begär ande typer som ska loggas. De tre möjliga typerna av begäran är **läsa**, **skriva**och **ta bort**. Om du vill stänga av loggning använder du värdet **none** för parametern **LoggingOperations** .  

 Följande kommando växlar vid loggning för läsnings-, skriv-och borttagnings begär anden i Kötjänst i ditt standard lagrings konto med kvarhållning inställt på fem dagar:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Följande kommando stänger av loggningen av tabell tjänsten på ditt standard lagrings konto:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Information om hur du konfigurerar Azure PowerShell-cmdletar så att de fungerar med din Azure-prenumeration och hur du väljer det standard lagrings konto som ska användas finns i: [så här installerar och konfigurerar du Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Aktivera lagrings loggning program mässigt  

 Förutom att använda Azure Portal eller Azure PowerShell-cmdletar för att kontrol lera lagrings loggning kan du också använda en av Azure Storage API: erna. Om du till exempel använder ett .NET-språk kan du använda lagrings klient biblioteket.  

 Klasserna **CloudBlobClient**, **CloudQueueClient**och **CloudTableClient** alla har metoder som **SetServiceProperties** och **SetServicePropertiesAsync** som tar ett **ServiceProperties** -objekt som en parameter. Du kan använda **ServiceProperties** -objektet för att konfigurera lagrings loggning. I följande C# kodfragment visas till exempel hur du ändrar vad som loggas och kvarhållningsperioden för att köa loggning:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Mer information om hur du använder ett .NET-språk för att konfigurera lagrings loggning finns i [referens för lagrings klient bibliotek](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Allmän information om hur du konfigurerar lagrings loggning med hjälp av REST API finns i [Aktivera och konfigurera Lagringsanalys](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Hämta logg data för lagrings loggning

 Om du vill visa och analysera loggdata bör du hämta de blobbar som innehåller de loggdata som du är intresse rad av för en lokal dator. Många verktyg för lagrings surfning gör att du kan ladda ned blobbar från ditt lagrings konto. Du kan också använda det Azure Storage team som angavs med kommando rads verktyget [AzCopy](storage-use-azcopy-v10.md) för Azure Copy för att hämta logg data.  
 
>[!NOTE]
> `$logs` container är inte integrerad med Event Grid, så du får inga meddelanden när loggfilerna skrivs. 

 För att se till att du hämtar loggdata som du är intresse rad av och för att undvika att ladda ned samma logg data mer än en gång:  

-   Använd namngivnings konventionen för datum och tid för blobbar som innehåller loggdata för att spåra vilka blobbar som du redan har laddat ned för analys för att undvika att ladda ned samma data mer än en gång.  

-   Använd metadata på Blobbarna som innehåller loggdata för att identifiera den specifika perioden som blobben innehåller loggdata för att identifiera den exakta blobben som du behöver hämta.  

Information om hur du kommer igång med AzCopy finns i [Kom igång med AZCopy](storage-use-azcopy-v10.md) 

I följande exempel visas hur du kan hämta logg data för Queue Service för timmar som börjar vid 09, 10 AM och 11 AM den 20 maj, 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Mer information om hur du hämtar vissa filer finns i [Hämta vissa filer](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files).

När du har hämtat dina loggdata kan du se logg posterna i filerna. Loggfilerna använder ett avgränsat text format som många logg läsnings verktyg kan tolka, inklusive Microsoft Message Analyzer (mer information finns i hand boken [övervakning, diagnostisering och fel sökning Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Olika verktyg har olika funktioner för formatering, filtrering, sortering och AD söker efter innehållet i dina loggfiler. Mer information om logg fils format och innehåll för lagrings loggning finns i [Lagringsanalys logg format](/rest/api/storageservices/storage-analytics-log-format) och [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Nästa steg

* [Lagringsanalys logg format](/rest/api/storageservices/storage-analytics-log-format)
* [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Lagringsanalys mått (klassisk)](storage-analytics-metrics.md)
