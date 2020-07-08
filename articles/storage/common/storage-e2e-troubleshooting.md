---
title: Fel sökning av data åtgärder med diagnostik och analys av meddelanden
titleSuffix: Azure Storage
description: En självstudie som demonstrerar fel sökning från slut punkt till slut punkt med Azure-lagringsanalys, AzCopy och Microsoft Message Analyzer
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: ozgun
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: e94810d53bf28aeb03de32a440c56a95232d0c2d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506352"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Felsökning från slutpunkt till slutpunkt med Azure Storage-mått och -loggning, AzCopy och Message Analyzer

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnostisering och fel sökning är en viktig kunskap för att skapa och stödja klient program med Microsoft Azure Storage. På grund av den distribuerade typen av ett Azure-program kan det vara mer komplicerat än i traditionella miljöer att diagnostisera och felsöka fel och prestanda problem.

I den här självstudien visar vi hur du kan identifiera vissa fel som kan påverka prestandan och felsöka felen från slut punkt till slut punkt med hjälp av verktyg från Microsoft och Azure Storage för att optimera klient programmet.

I den här självstudien får du en praktisk utforskning av ett scenario för fel sökning från slut punkt till slut punkt. En djupgående konceptuell guide för att felsöka Azure Storage-program finns i [övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Verktyg för fel sökning av Azure Storage program

Om du vill felsöka klient program med hjälp av Microsoft Azure Storage kan du använda en kombination av verktyg för att avgöra när ett problem har inträffat och vad orsaken till problemet kan vara. Dessa verktyg innefattar:

* **Azure-lagringsanalys**. [Azure-lagringsanalys](/rest/api/storageservices/Storage-Analytics) tillhandahåller mått och loggning för Azure Storage.

  * **Lagrings mått** spårar transaktions mått och kapacitets mått för ditt lagrings konto. Med hjälp av mått kan du bestämma hur programmet ska utföras enligt olika mått. Se [Lagringsanalys Metrics tabell schema](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) för mer information om de typer av mått som spåras av Lagringsanalys.
  * **Lagrings loggning** loggar varje begäran till Azure Storage-tjänsterna till en logg på Server sidan. Loggen spårar detaljerade data för varje begäran, inklusive den utförda åtgärden, status för åtgärden och latens information. Se [Lagringsanalys logg format](/rest/api/storageservices/Storage-Analytics-Log-Format) för mer information om begär ande-och svars data som skrivs till loggarna av Lagringsanalys.

* **Azure Portal**. Du kan konfigurera mått och loggning för ditt lagrings konto i [Azure Portal](https://portal.azure.com). Du kan också visa diagram och diagram som visar hur programmet presterar över tid och konfigurera aviseringar för att meddela dig om programmet fungerar annorlunda än förväntat för ett angivet mått.

    Se [övervaka ett lagrings konto i Azure Portal](storage-monitor-storage-account.md) för information om hur du konfigurerar övervakning i Azure Portal.
* **AzCopy**. Server loggar för Azure Storage lagras som blobbar, så du kan använda AzCopy för att kopiera logg-blobar till en lokal katalog för analys med Microsoft Message Analyzer. Mer information om AzCopy finns i [överföra data med kommando rads verktyget AzCopy](storage-use-azcopy.md) .
* **Microsoft Message Analyzer**. Message Analyzer är ett verktyg som använder loggfiler och visar loggdata i ett visuellt format som gör det enkelt att filtrera, söka i och gruppera logg data i användbara uppsättningar som du kan använda för att analysera fel-och prestanda problem. Mer information om Message Analyzer finns i [funktions guiden för Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx) .

## <a name="about-the-sample-scenario"></a>Om exempel scenariot

I den här självstudien granskar vi ett scenario där Azure Storage mått anger en låg procent andel lyckade för ett program som anropar Azure Storage. Måttet för låg procents frekvens i procent (visas som **PercentSuccess** i [Azure Portal](https://portal.azure.com) och i mått tabeller) spårar åtgärder som lyckas, men som returnerar en HTTP-statuskod som är större än 299. I loggfilerna för lagring på Server sidan registreras dessa åtgärder med transaktions statusen **ClientOtherErrors**. Mer information om måttet låg procents ATS finns i [måtten Visa poster med låg PercentSuccess eller Analytics-logg har åtgärder med transaktions status ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure Storage åtgärder kan returnera HTTP-statuskod som är större än 299 som en del av deras normala funktion. Men dessa fel i vissa fall indikerar att du kan optimera ditt klient program för att få bättre prestanda.

I det här scenariot ska vi ta en låg procent andel av framgång som är allt lägre än 100%. Du kan dock välja en annan mått nivå, beroende på dina behov. Vi rekommenderar att du under testning av ditt program upprättar en bas linje tolerans för dina viktiga prestanda mått. Du kan till exempel bestämma, baserat på testning, att ditt program ska ha en konsekvent procents ATS på 90% eller 85%. Om dina mått data visar att programmet är deviating från det numret kan du undersöka vad som kan orsaka ökningen.

När vi har upp100 rättat ett exempel scenario, kommer vi att undersöka loggarna för att hitta de fel som motsvarar måtten och använda dem för att ta reda på vad som orsakar lägre procent andel lyckade. Vi ska titta närmare på fel i 400-intervallet. Sedan undersöker vi 404-fel (hittades inte).

### <a name="some-causes-of-400-range-errors"></a>Några orsaker till 400-intervall fel

I exemplen nedan visas en sampling av vissa 400-intervall-fel för begär Anden mot Azure Blob Storage och deras möjliga orsaker. Alla dessa fel, samt fel i intervallet 300 och 500-intervallet, kan bidra till en låg procent andel lyckade.

Observera att listorna nedan är långt från att slutföras. Se [status-och felkoder](https://msdn.microsoft.com/library/azure/dd179382.aspx) på MSDN för information om allmänna Azure Storage fel och om fel som är speciella för var och en av lagrings tjänsterna.

#### <a name="status-code-404-not-found-examples"></a>Status kod 404 (hittades inte)-exempel

Inträffar när en Läs åtgärd mot en container eller BLOB Miss lyckas eftersom blobben eller containern inte hittas.

* Inträffar om en behållare eller BLOB har tagits bort av en annan klient före denna begäran.
* Inträffar om du använder ett API-anrop som skapar behållaren eller blobben när du har kontrollerat om det finns. CreateIfNotExists-API: er gör ett huvud anrop först för att söka efter förekomsten av containern eller blobben. om det inte finns returneras ett 404-fel och ett andra anrop görs för att skriva behållaren eller blobben.

#### <a name="status-code-409-conflict-examples"></a>Exempel på status kod 409 (konflikt)

* Inträffar om du använder ett skapa-API för att skapa en ny behållare eller BLOB, utan att först söka efter den, och det finns redan en behållare eller BLOB med det namnet.
* Inträffar om en behållare tas bort och du försöker skapa en ny behållare med samma namn innan borttagnings åtgärden har slutförts.
* Inträffar om du anger ett lån på en behållare eller BLOB och det redan finns ett lån.

#### <a name="status-code-412-precondition-failed-examples"></a>Status kod 412 (villkoret misslyckades) exempel

* Inträffar när villkoret som anges av ett villkors huvud inte har uppfyllts.
* Inträffar när det angivna låne-ID: t inte matchar låne-ID: t för containern eller blobben.

## <a name="generate-log-files-for-analysis"></a>Generera loggfiler för analys

I den här självstudien använder vi Message Analyzer för att arbeta med tre olika typer av loggfiler, men du kan välja att arbeta med något av följande:

* **Server loggen**, som skapas när du aktiverar Azure Storage loggning. Server loggen innehåller information om varje åtgärd som anropas mot en av Azure Storage Services – BLOB, kö, tabell och fil. Server loggen visar vilken åtgärd som anropades och vilken status kod som returnerades, samt annan information om begäran och svar.
* **.Net-klient loggen**, som skapas när du aktiverar loggning på klient sidan från ditt .NET-program. Klient loggen innehåller detaljerad information om hur klienten förbereder begäran och tar emot och bearbetar svaret.
* **Spårnings loggen för http-nätverk**, som samlar in data på http/https-begäran och svars data, inklusive för åtgärder mot Azure Storage. I den här självstudien genererar vi nätverks spårning via Message Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurera loggning och mått på Server Sidan

Först måste vi konfigurera Azure Storage loggning och mått, så att vi kan analysera data från tjänst sidan. Du kan konfigurera loggning och mått på flera olika sätt – via [Azure Portal](https://portal.azure.com), genom att använda PowerShell eller program mässigt. Se [Aktivera mått](storage-analytics-metrics.md#enable-metrics-by-using-the-azure-portal) och [Aktivera loggning](storage-analytics-logging.md#enable-storage-logging) för information om hur du konfigurerar loggning och mått.

### <a name="configure-net-client-side-logging"></a>Konfigurera loggning av .NET-klient Sidan

Aktivera .NET Diagnostics i programmets konfigurations fil (web.config eller app.config) om du vill konfigurera loggning på klient sidan för ett .NET-program. Mer information finns i [Logga in på klient sidan med .net-lagrings klient biblioteket](https://msdn.microsoft.com/library/azure/dn782839.aspx) och [loggning på klient sidan med Microsoft Azure Storage SDK för Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) på MSDN.

Loggen på klient sidan innehåller detaljerad information om hur klienten förbereder begäran och tar emot och bearbetar svaret.

Lagrings klient biblioteket lagrar logg data på klient sidan på den plats som anges i programmets konfigurations fil (web.config eller app.config).

### <a name="collect-a-network-trace"></a>Samla in ett nätverks spår

Du kan använda Message Analyzer för att samla in en HTTP/HTTPS-nätverks spårning medan ditt klient program körs. Message Analyzer använder [Fiddler](https://www.telerik.com/fiddler) på Server delen. Innan du samlar in nätverks spårningen rekommenderar vi att du konfigurerar Fiddler för att registrera okrypterad HTTPS-trafik:

1. Installera [Fiddler](https://www.telerik.com/download/fiddler).
2. Starta Fiddler.
3. Välj **verktyg | Fiddler alternativ**.
4. I dialog rutan alternativ kontrollerar du att **avbildnings-https ansluter** och **dekrypterar HTTPS-trafik** båda är markerade, som du ser nedan.

![Konfigurera Fiddler-alternativ](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

För självstudien samlar du in och sparar en nätverks spårning först i Message Analyzer och skapar sedan en Analysis-session för att analysera spårningen och loggarna. Så här samlar du in en nätverks spårning i Message Analyzer:

1. I Message Analyzer väljer du **fil | Snabb spårning | Okrypterad HTTPS**.
2. Spårningen påbörjas omedelbart. Välj **stoppa** för att stoppa spårningen så att vi kan konfigurera den att enbart spåra lagrings trafik.
3. Redigera spårningssessionen genom att välja **Redigera** .
4. Välj länken **Konfigurera** till höger om **Microsoft-PEF-WebProxy ETW-** providern.
5. I dialog rutan **Avancerade inställningar** klickar du på fliken **Provider** .
6. I fältet **hostname filter** anger du dina lagrings slut punkter, avgränsade med blank steg. Du kan till exempel ange dina slut punkter enligt följande: ändra `storagesample` till namnet på ditt lagrings konto:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Stäng dialog rutan och klicka på **starta om** för att börja samla in spårningen med hostname-filtret på plats, så att endast Azure Storage nätverks trafik ingår i spårningen.

> [!NOTE]
> När du är färdig med att samla in din nätverks spårning rekommenderar vi starkt att du återställer de inställningar som du kan ha ändrat i Fiddler för att dekryptera HTTPS-trafik. I dialog rutan Fiddler alternativ avmarkerar du kryss rutorna **avbilda https ansluter** och **DEkrypterar HTTPS-trafik** .

Mer information finns i [använda funktionerna för nätverks spårning](https://technet.microsoft.com/library/jj674819.aspx) på TechNet.

## <a name="review-metrics-data-in-the-azure-portal"></a>Granska mått data i Azure Portal

När ditt program har körts under en viss tids period kan du granska mått diagram som visas i [Azure Portal](https://portal.azure.com) för att se hur tjänsten har utförts.

Börja med att navigera till ditt lagrings konto i Azure Portal. Som standard visas ett övervaknings diagram med måttet **slutförd procent** på konto bladet. Om du tidigare har ändrat diagrammet för att visa olika mått, lägger du till måttet **slutförd procent** .

Nu visas **procent andelen lyckade** i övervaknings diagrammet, tillsammans med alla andra mått som du kan ha lagt till. I det scenario vi ska undersöka härnäst genom att analysera loggarna i Message Analyzer, är procent andelen lyckade under 100%.

Mer information om hur du lägger till och anpassar mått diagram finns i [Anpassa mått diagram](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Det kan ta lite tid innan dina mått data visas i Azure Portal när du har aktiverat lagrings mått. Detta beror på att Tim mått för föregående timme inte visas i Azure Portal förrän den aktuella timmen har förflutit. Dessutom visas inte minut mått för närvarande i Azure Portal. Det kan ta upp till två timmar att se mått data, beroende på när du aktiverar mått.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Använda AzCopy för att kopiera Server loggar till en lokal katalog

Azure Storage skriver serverns loggdata till blobbar, medan mått skrivs till tabeller. Log-blobbar är tillgängliga i den välkända `$logs` behållaren för ditt lagrings konto. Log-blobbar namnges hierarkiskt efter år, månad, dag och timme, så att du enkelt kan hitta det tidsintervall som du vill undersöka. I kontot är till exempel `storagesample` behållaren för log-Blobbarna för 01/02/2015, från 8-9 am, `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800` . De enskilda Blobbarna i den här behållaren namnges sekventiellt, från och med `000000.log` .

Du kan använda kommando rads verktyget AzCopy för att hämta dessa loggfiler på Server sidan till en valfri plats på den lokala datorn. Du kan till exempel använda följande kommando för att ladda ned loggfilerna för BLOB-åtgärder som ägde rum den 2 januari 2015 till mappen `C:\Temp\Logs\Server` . Ersätt `<storageaccountname>` med namnet på ditt lagrings konto:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

AzCopy kan hämtas på sidan för [Azure-nedladdningar](https://azure.microsoft.com/downloads/) . Mer information om hur du använder AzCopy finns i [överföra data med kommando rads verktyget AzCopy](storage-use-azcopy.md).

Mer information om hur du hämtar loggar på Server sidan finns i [Hämta logg data för lagrings loggning](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Analysera loggdata med Microsoft Message Analyzer

Microsoft Message Analyzer är ett verktyg för att samla in, Visa och analysera protokoll meddelande trafik, händelser och andra system-eller program meddelanden i fel söknings-och diagnostiska scenarier. Med Message Analyzer kan du också läsa in, sammanställa och analysera data från loggar och sparade spårningsfiler. Mer information om Message Analyzer finns i [funktions guide för Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

Message Analyzer innehåller till gångar för Azure Storage som hjälper dig att analysera Server-, klient-och nätverks loggar. I det här avsnittet diskuterar vi hur du använder dessa verktyg för att lösa problemet med låg procents ATS i lagrings loggarna.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Hämta och installera Message Analyzer och Azure Storage till gångar

1. Ladda ned [Message Analyzer](https://docs.microsoft.com/message-analyzer/installing-and-upgrading-message-analyzer).
2. Starta Message Analyzer.
3. Från **verktyg** -menyn väljer du **till gångs hanteraren**. I dialog rutan **till gångs hanteraren** väljer du **hämtningar**och filtrerar sedan **Azure Storage**. Du ser Azure Storage till gångar, som du ser i bilden nedan.
4. Klicka på **synkronisera alla visade objekt** för att installera Azure Storage till gångar. Tillgängliga till gångar inkluderar:
   * **Azure Storage färg regler:** Med Azure Storage färg regler kan du definiera särskilda filter som använder färg-, text-och teckensnitts stilar för att markera meddelanden som innehåller specifik information i en spårning.
   * **Azure Storage diagram:** Azure Storage diagram är fördefinierade diagram som data i Graph Server-loggen. Observera att om du vill använda Azure Storage diagram just nu kan du bara läsa in Server loggen i analys rutnätet.
   * **Azure Storage tolkare:** Azure Storage Parsers parsar Azure Storage klient-, server-och HTTP-loggar för att visa dem i analys rutnätet.
   * **Azure Storage filter:** Azure Storage filter är fördefinierade villkor som du kan använda för att fråga data i analys rutnätet.
   * **Azure Storage Visa layouter:** Azure Storage se layouter är fördefinierade kolumnlayouter och grupperingar i analys rutnätet.
5. Starta om Message Analyzer när du har installerat till gångarna.

![Message Analyzer-Asset Manager](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Installera alla Azure Storage till gångar som visas i den här självstudien.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Importera dina loggfiler till Message Analyzer

Du kan importera alla sparade loggfiler (Server sidan, klient sidan och nätverket) till en enda session i Microsoft Message Analyzer för analys.

1. Klicka på **ny session**på **Arkiv** -menyn i Microsoft Message Analyzer och klicka sedan på **Tom session**. I dialog rutan **ny session** anger du ett namn för din Analysis-session. Klicka på knappen **filer** på panelen **sessionsinformation** .
2. Om du vill läsa in de nätverks spårnings data som genereras av Message Analyzer klickar du på **Lägg till filer**, bläddrar till den plats där du sparade. matp-filen från webbspårning-sessionen, väljer. matp-filen och klickar på **Öppna**.
3. Läs in Server sidans loggdata genom att klicka på **Lägg till filer**, bläddra till den plats där du laddade ned dina loggar på Server sidan, Välj loggfilerna för det tidsintervall som du vill analysera och klicka på **Öppna**. I panelen **sessionsinformation** anger du sedan List rutan **text logg konfiguration** för varje logg fil på Server sidan till **AzureStorageLog** för att säkerställa att Microsoft Message Analyzer kan tolka logg filen korrekt.
4. Om du vill läsa in loggdata på klient sidan klickar du på **Lägg till filer**, bläddrar till den plats där du sparade loggar på klient sidan, väljer de loggfiler som du vill analysera och klickar på **Öppna**. I panelen **sessionsinformation** anger du sedan List rutan **text logg konfiguration** för varje logg fil på klient sidan till **AzureStorageClientDotNetV4** för att säkerställa att Microsoft Message Analyzer kan tolka logg filen korrekt.
5. Klicka på **Starta** i dialog rutan **ny session** för att läsa in och parsa loggdata. Loggdata visas i rutnätet analys verktyg för analys.

Bilden nedan visar en exempel-session som kon figurer ATS med Server-, klient-och nätverks spårnings loggar.

![Konfigurera Message Analyzer-session](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Observera att Message Analyzer läser in loggfiler i minnet. Om du har en stor uppsättning loggdata ska du filtrera den för att få bästa möjliga prestanda från analys verktyget.

Börja med att bestämma den tidsram som du är intresse rad av och se till att den här tids ramen är så liten som möjligt. I många fall vill du bara granska en period på några minuter eller timmar. Importera den minsta uppsättningen loggar som kan uppfylla dina behov.

Om du fortfarande har en stor mängd loggdata kanske du vill ange ett sessions filter för att filtrera dina loggdata innan du läser in dem. I rutan **session filter** väljer du knappen **bibliotek** för att välja ett fördefinierat filter. Välj till exempel **globalt tids filter** i från Azure Storage filter för att filtrera efter ett tidsintervall. Du kan sedan Redigera filter villkoren för att ange start-och slut tids stämpling för det intervall som du vill se. Du kan också filtrera efter en viss status kod. Du kan till exempel välja att bara läsa in logg poster där status koden är 404.

Mer information om hur du importerar loggdata till Microsoft Message Analyzer finns i [Hämta meddelande data](https://technet.microsoft.com/library/dn772437.aspx) på TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Använd klient förfrågnings-ID: t för att korrelera logg fils data

Azure Storage klient biblioteket genererar automatiskt ett unikt ID för klient förfrågan för varje begäran. Det här värdet skrivs till klient loggen, Server loggen och nätverks spårningen, så att du kan använda den för att korrelera data mellan alla tre loggar i Message Analyzer. Se [ID för klientbegäran](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) om du vill ha mer information om klient förfrågnings-ID: t.

I avsnitten nedan beskrivs hur du använder förkonfigurerade och anpassade vyer för att korrelera och gruppera data baserat på klientens förfrågnings-ID.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Välj en visnings-layout som ska visas i analys rutnätet

Lagrings resurserna för Message Analyzer innehåller Azure Storage Visa layouter, som är förkonfigurerade vyer som du kan använda för att visa dina data med användbara grupperingar och kolumner för olika scenarier. Du kan också skapa anpassade vyer och spara dem för åter användning.

I bilden nedan visas menyn **Visa layout** , som är tillgänglig genom att välja **Visa layout** i menyfliksområdet verktygsfält. Vyerna för Azure Storage grupperas under noden **Azure Storage** på menyn. Du kan söka efter `Azure Storage` i rutan Sök för att filtrera på Azure Storage endast Visa layouter. Du kan också välja stjärnan bredvid en visnings-layout för att göra den till en favorit och visa den överst på menyn.

![Visa Layout-menyn](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Börja med genom att välja **grupperat efter ClientRequestID och modul**. I den här vyn visas logg data från alla tre loggarna först efter klient-ID för begäran, sedan efter käll logg fil (eller **modul** i Message Analyzer). I den här vyn kan du öka detalj nivån för ett visst ID för klientbegäran och se data från alla tre loggfiler för klientens begär ande-ID.

I bilden nedan visas den här vyn som används i exempel logg data, med en delmängd av kolumner som visas. Du kan se att för ett visst klient förfrågnings-ID visas data från klient loggen, Server loggen och nätverks spårningen i analys rutnätet.

![Azure Storage vy-layout](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Olika loggfiler har olika kolumner, så när data från flera loggfiler visas i analys rutnätet får vissa kolumner inte innehålla några data för en viss rad. I bilden ovan visar till exempel klient logg raderna inga data för kolumnerna **timestamp**, **TimeElapsed**, **Source**och **destination** , eftersom dessa kolumner inte finns i klient loggen, men finns i nätverks spårningen. På samma sätt visar kolumnen **tidsstämpelkolumn** tidsstämpel-data från Server loggen, men inga data visas för kolumnerna **TimeElapsed**, **Source**och **destination** , som inte ingår i Server loggen.
>
>

Förutom att använda Azure Storage Visa layouter kan du också definiera och spara dina egna vyer. Du kan också välja andra önskade fält för gruppering av data och spara grupperingen som en del av din anpassade layout.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Använd färg regler för analys rutnätet

Lagrings resurserna innehåller också färg regler som ger ett visuellt sätt att identifiera olika typer av fel i analys rutnätet. De fördefinierade färg reglerna gäller HTTP-fel, så de visas bara för Server loggen och nätverks spårningen.

Om du vill använda färg regler väljer du **färg regler** i menyfliksområdet verktygsfält. Du ser Azure Storage färg regler på menyn. I självstudien väljer du **klient fel (StatusCode mellan 400 och 499)**, som du ser i bilden nedan.

![Azure Storage vy-layout](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Förutom att använda Azure Storage färg regler kan du också definiera och spara egna färg regler.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Gruppera och filtrera logg data för att hitta 400-intervall fel

Sedan ska vi gruppera och filtrera loggdata för att hitta alla fel i 400-intervallet.

1. Leta upp kolumnen **StatusCode** i analys rutnätet, högerklicka på kolumn rubriken och välj **grupp**.
2. Gruppera sedan efter i kolumnen **ClientRequestId** . Du ser att data i analys rutnätet nu är ordnade efter status kod och efter klient förfrågnings-ID.
3. Visa fönstret för visnings filter om det inte redan visas. I menyfliksområdet verktygsfält väljer du **verktygs fönster**och sedan **Visa filter**.
4. Om du vill filtrera loggdata så att endast 400-intervall fel visas lägger du till följande filter villkor i fönstret **Visa filter** och klickar på **Använd**:

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

Bilden nedan visar resultatet av den här grupperingen och filtret. Om du expanderar fältet **ClientRequestID** under grupperingen för status kod 409, till exempel, visas en åtgärd som resulterade i status koden.

![Azure Storage vy-layout](./media/storage-e2e-troubleshooting/400-range-errors1.png)

När du har tillämpat det här filtret ser du att rader från klient loggen utesluts, eftersom klient loggen inte innehåller kolumnen **StatusCode** . För att börja med granskar vi Server-och nätverks spårnings loggarna för att hitta 404-fel och sedan återgår vi till klient loggen för att undersöka de klient åtgärder som ledde till dem.

> [!NOTE]
> Du kan filtrera efter kolumnen **StatusCode** och fortfarande visa data från alla tre loggarna, inklusive klient loggen, om du lägger till ett uttryck i filtret som innehåller logg poster där status koden är null. Använd följande för att skapa det här filter uttrycket:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Det här filtret returnerar alla rader från klient loggen och endast rader från Server loggen och HTTP-loggen där status koden är större än 400. Om du använder den i vyn layout grupperad efter ID och modul för klientbegäran, kan du söka i eller bläddra igenom logg posterna för att hitta dem där alla tre loggarna visas.

### <a name="filter-log-data-to-find-404-errors"></a>Filtrera loggdata för att hitta 404-fel

Lagrings resurserna innehåller fördefinierade filter som du kan använda för att begränsa loggdata för att hitta de fel eller trender som du letar efter. Nu ska vi tillämpa två fördefinierade filter: en som filtrerar Server-och nätverks spårnings loggarna för 404 fel och en som filtrerar data i ett angivet tidsintervall.

1. Visa fönstret för visnings filter om det inte redan visas. I menyfliksområdet verktygsfält väljer du **verktygs fönster**och sedan **Visa filter**.
2. I fönstret Visa filter väljer du **bibliotek**och söker `Azure Storage` efter Azure Storage filter. Välj filtret för **404-meddelanden (hittades inte) i alla loggar**.
3. Visa **biblioteks** menyn igen och leta upp och välj det **globala tids filtret**.
4. Redigera de tidsstämplar som visas i filtret för det intervall som du vill visa. På så sätt kan du begränsa det data intervall som ska analyseras.
5. Filtret bör se ut ungefär som i exemplet nedan. Tillämpa filtret i analys rutnätet genom att klicka på **Använd** .

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Azure Storage vy-layout](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analysera dina loggdata

Nu när du har grupperat och filtrerat dina data kan du granska information om enskilda förfrågningar som genererade 404-fel. I den aktuella vyns layout grupperas data efter klient-ID för begäran, sedan efter logg källa. Eftersom vi filtrerar på begär Anden där fältet StatusCode innehåller 404, ser vi bara server-och nätverks spårnings data, inte klientens loggdata.

Bilden nedan visar en speciell begäran där en get BLOB-åtgärd gav en 404 eftersom blobben inte finns. Observera att vissa kolumner har tagits bort från standardvyn för att visa relevanta data.

![Filtrerade Server-och nätverks spårnings loggar](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Därefter korrelerar vi detta ID för klientbegäran med klient logg data för att se vilka åtgärder klienten tog när felet inträffade. Du kan visa en ny Analysis Grid-vy för den här sessionen om du vill visa klient logg data som öppnas på en andra flik:

1. Kopiera först värdet för fältet **ClientRequestId** till Urklipp. Du kan göra detta genom att välja någon av raderna, leta upp fältet **ClientRequestId** , högerklicka på datavärdet och välja **Kopiera ' ClientRequestId '**.
2. I menyfliksområdet verktygsfält väljer du **ny Viewer**och väljer sedan **analys rutnät** för att öppna en ny flik. Den nya fliken visar alla data i dina loggfiler, utan gruppering, filtrering eller färg regler.
3. I menyfliksområdet verktygsfält väljer du **Visa layout**och väljer sedan **alla .net-klient kolumner** i avsnittet **Azure Storage** . I den här vyn visas data från klient loggen samt Server-och nätverks spårnings loggarna. Som standard sorteras den i kolumnen **MessageNumber** .
4. Sök sedan efter klient förfrågan-ID i klient loggen. I menyfliksområdet verktygsfält väljer du **Sök meddelanden**och anger sedan ett anpassat filter för klient förfrågnings-ID: t i fältet **Sök** . Använd den här syntaxen för filtret och ange ditt eget ID för klientbegäran:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

Message Analyzer söker efter och väljer den första logg posten där Sök kriterierna matchar ID för klientbegäran. I klient loggen finns det flera poster för varje klient förfrågnings-ID, så du kanske vill gruppera dem i fältet **ClientRequestId** för att göra det lättare att se dem tillsammans. I bilden nedan visas alla meddelanden i klient loggen för det angivna ID: t för klient förfrågan.

![Klient loggen visar 404 fel](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Med hjälp av de data som visas i vyerna i dessa två flikar kan du analysera begär ande data för att avgöra vad som kan ha orsakat felet. Du kan också titta på begär Anden som föregår denna för att se om en tidigare händelse kan leda till 404-felet. Du kan till exempel granska posterna i klient loggen före detta ID för klientbegäran för att avgöra om blobben kan ha tagits bort eller om felet berodde på att klient programmet anropade ett CreateIfNotExists-API på en behållare eller BLOB. I klient loggen kan du hitta blobb adressen i fältet **Beskrivning** ; i Server-och nätverks spårnings loggarna visas den här informationen i fältet **Sammanfattning** .

När du känner till adressen till blobben som gav upphov till 404-felet kan du undersöka ytterligare. Om du söker i logg poster efter andra meddelanden som är kopplade till åtgärder på samma BLOB kan du kontrol lera om klienten tidigare tog bort entiteten.

## <a name="analyze-other-types-of-storage-errors"></a>Analysera andra typer av lagrings fel

Nu när du har lärt dig att använda Message Analyzer för att analysera dina loggdata kan du analysera andra typer av fel med hjälp av Visa layouter, färg regler och sökning/filtrering. I tabellerna nedan visas några problem som kan uppstå och filter villkor som du kan använda för att hitta dem. Mer information om hur du skapar filter och filter språket för Message Analyzer finns i [filtrera meddelande data](https://technet.microsoft.com/library/jj819365.aspx).

| Att undersöka... | Använd filter uttryck... | Uttrycket gäller för loggen (klient, Server, nätverk, alla) |
| --- | --- | --- |
| Oväntade fördröjningar i meddelande leverans i en kö |AzureStorageClientDotNetV4. Description innehåller "försök att försöka igen." |Klient |
| HTTP-ökning i PercentThrottlingError |Inkommande. Response. StatusCode = = 500 &#124;&#124; HTTP. Response. StatusCode = = 503 |Nätverk |
| Ökning i PercentTimeoutError |Inkommande. Response. StatusCode = = 500 |Nätverk |
| Ökning i PercentTimeoutError (alla) |* StatusCode = = 500 |Alla |
| Ökning i PercentNetworkError |AzureStorageClientDotNetV4. EventLogEntry. level < 2 |Klient |
| HTTP 403-meddelanden (Förbjudet) |Inkommande. Response. StatusCode = = 403 |Nätverk |
| HTTP 404-meddelanden (Hittades inte) |Inkommande. Response. StatusCode = = 404 |Nätverk |
| 404 (alla) |* StatusCode = = 404 |Alla |
| Det är problem med SAS-autentiseringen (signatur för delad åtkomst) |AzureStorageLog. RequestStatus = = "SASAuthorizationError" |Nätverk |
| HTTP 409-meddelanden (Konflikt) |Inkommande. Response. StatusCode = = 409 |Nätverk |
| 409 (alla) |* StatusCode = = 409 |Alla |
| Låga PercentSuccess-eller analys logg poster har åtgärder med transaktions status ClientOtherErrors |AzureStorageLog. RequestStatus = = "ClientOtherError" |Server |
| Nagle-varning |((AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS) > (AzureStorageLog. ServerLatencyMS * 1,5)) och (AzureStorageLog. RequestPacketSize <1460) och (AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS >= 200) |Server |
| Tidsintervall i Server-och nätverks loggar |#Timestamp >= 2014-10-20T16:36:38 och #Timestamp <= 2014-10-20T16:36:39 |Server, nätverk |
| Tidsintervall i Server loggar |AzureStorageLog. tidsstämpel >= 2014-10-20T16:36:38 och AzureStorageLog. tidsstämpel <= 2014-10-20T16:36:39 |Server |

## <a name="next-steps"></a>Nästa steg

Mer information om fel sökning av scenarier från slut punkt till slut punkt i Azure Storage finns i följande resurser:

* [Övervaka, diagnostisera och felsök Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Lagringsanalys](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Övervaka ett lagringskonto i Azure-portalen](storage-monitor-storage-account.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Microsoft Message Analyzer – användningsguide](https://technet.microsoft.com/library/jj649776.aspx)
