---
title: Felsöka dataåtgärder med diagnostik och Meddelandeanalys
titleSuffix: Azure Storage
description: En självstudiekurs som visar felsökning från på nytt med Azure Storage Analytics, AzCopy och Microsoft Message Analyzer
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 69983502fb7d099f474fb1c4c084f5d381a173e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314767"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Felsökning från slutpunkt till slutpunkt med Azure Storage-mått och -loggning, AzCopy och Message Analyzer

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Att diagnostisera och felsöka är en viktig färdighet för att skapa och stödja klientprogram med Microsoft Azure Storage. På grund av den distribuerade karaktären av ett Azure-program kan det vara mer komplicerat att diagnostisera och felsöka fel och prestandaproblem än i traditionella miljöer.

I den här självstudien visar vi hur du identifierar vissa fel som kan påverka prestanda och felsöker dessa fel från heltäckande verktyg som tillhandahålls av Microsoft och Azure Storage, för att optimera klientprogrammet.

Den här självstudien ger en praktisk utforskning av ett felsökningsscenario från på alla dagar. En detaljerad konceptuell guide för felsökning av Azure-lagringsprogram finns i [Övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Verktyg för felsökning av Azure Storage-program

Om du vill felsöka klientprogram med Microsoft Azure Storage kan du använda en kombination av verktyg för att avgöra när ett problem har uppstått och vad problemet kan vara. Dessa verktyg innefattar:

* **Azure Storage Analytics**. [Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) tillhandahåller mått och loggning för Azure Storage.

  * **Lagringsmått** spårar transaktionsmått och kapacitetsmått för ditt lagringskonto. Med hjälp av mått kan du bestämma hur ditt program fungerar enligt en mängd olika mått. Mer information om vilka typer av mått som spåras av Storage Analytics finns i Tabellschema för [Lagringsanalys.](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)
  * **Lagringsloggning** loggar varje begäran till Azure Storage-tjänsterna till en serverlogg. Loggen spårar detaljerade data för varje begäran, inklusive den utförda åtgärden, åtgärdens status och svarstidsinformation. Mer information om begäran och svarsdata som skrivs till loggarna från Storage Analytics finns i [Loggformat](/rest/api/storageservices/Storage-Analytics-Log-Format) för lagringsanalys.

* **Azure-portal**. Du kan konfigurera mått och loggning för ditt lagringskonto i [Azure-portalen](https://portal.azure.com). Du kan också visa diagram och diagram som visar hur programmet fungerar över tid och konfigurera aviseringar för att meddela dig om ditt program fungerar annorlunda än förväntat för ett angivet mått.

    Mer information om hur du konfigurerar övervakning i Azure-portalen finns [i Övervaka ett lagringskonto i Azure-portalen.](storage-monitor-storage-account.md)
* **AzCopy**. Serverloggar för Azure Storage lagras som blobbar, så du kan använda AzCopy för att kopiera loggblobar till en lokal katalog för analys med Microsoft Message Analyzer. Mer information om AzCopy finns i [Överföra data med kommandoradsverktyget AzCopy.](storage-use-azcopy.md)
* **Microsoft Message Analyzer**. Message Analyzer är ett verktyg som använder loggfiler och visar loggdata i ett visuellt format som gör det enkelt att filtrera, söka och gruppera loggdata till användbara uppsättningar som du kan använda för att analysera fel och prestandaproblem. Mer information om Message [Analyzer finns](https://technet.microsoft.com/library/jj649776.aspx) i bruksanvisningen för Microsoft Message Analyzer.

## <a name="about-the-sample-scenario"></a>Om exempelscenariot

För den här självstudien undersöker vi ett scenario där Azure Storage-mått indikerar en låg procentig framgång för ett program som anropar Azure-lagring. Måttet med låg procentig framgång (visas som **Procentsuccess** i [Azure-portalen](https://portal.azure.com) och i måtttabellerna) spårar åtgärder som lyckas, men som returnerar en HTTP-statuskod som är större än 299. I lagringsloggfilerna på serversidan registreras dessa åtgärder med transaktionsstatus **för ClientOtherErrors**. Mer information om måttet med låg procentig framgång finns i [Mått visar låga procentresultat eller analysloggposter har operationer med transaktionsstatus för ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure Storage-åtgärder kan returnera HTTP-statuskoder som är större än 299 som en del av deras normala funktionalitet. Men dessa fel i vissa fall tyder på att du kanske kan optimera klientprogrammet för bättre prestanda.

I det här scenariot kommer vi att betrakta en låg procentig framgång vara något under 100%. Du kan dock välja en annan måttnivå enligt dina behov. Vi rekommenderar att du under testningen av ditt program upprättar en baslinjetolerans för dina nyckeltal. Du kan till exempel bestämma, baserat på testning, att ditt program ska ha en konsekvent procentig framgång på 90 % eller 85 %. Om dina mätdata visar att programmet avviker från det numret kan du undersöka vad som kan orsaka ökningen.

När vi har fastställt att måttet för procentframgång är under 100 % för vårt exempelscenario undersöker vi loggarna för att hitta de fel som korrelerar med måtten och använder dem för att ta reda på vad som orsakar den lägre procentframgångsfrekvensen. Vi tittar specifikt på fel i 400-området. Då ska vi närmare undersöka 404 (Hittades) fel.

### <a name="some-causes-of-400-range-errors"></a>Vissa orsaker till 400-intervallfel

Exemplen nedan visar ett urval av cirka 400 intervallfel för begäranden mot Azure Blob Storage och deras möjliga orsaker. Något av dessa fel, samt fel i 300-intervallet och 500-intervallet, kan bidra till en låg procentig framgång.

Observera att listorna nedan är långt ifrån fullständiga. Se [Status- och felkoder](https://msdn.microsoft.com/library/azure/dd179382.aspx) på MSDN för information om allmänna Azure Storage-fel och om fel som är specifika för var och en av lagringstjänsterna.

#### <a name="status-code-404-not-found-examples"></a>Exempel på statuskod 404 (hittades inte)

Inträffar när en läsåtgärd mot en behållare eller blob misslyckas eftersom blob eller behållaren inte hittas.

* Inträffar om en behållare eller blob har tagits bort av en annan klient före den här begäran.
* Inträffar om du använder ett API-anrop som skapar behållaren eller bloben efter att ha kontrollerat om den finns. Api:erna CreateIfNotExists gör ett HEAD-anrop först för att kontrollera om det finns en behållare eller blob. Om det inte finns returneras ett 404-fel och sedan görs ett andra PUT-anrop för att skriva behållaren eller bloben.

#### <a name="status-code-409-conflict-examples"></a>Exempel på statuskod 409 (konflikt)

* Inträffar om du använder ett Skapa API för att skapa en ny behållare eller blob, utan att söka efter existens först, och det redan finns en behållare eller blob med det namnet.
* Inträffar om en behållare tas bort och du försöker skapa en ny behållare med samma namn innan borttagningen är klar.
* Inträffar om du anger ett lån på en behållare eller blob och det redan finns ett lån.

#### <a name="status-code-412-precondition-failed-examples"></a>Exempel på statuskod 412 (förutsättning misslyckades)

* Inträffar när villkoret som anges av ett villkorligt huvud inte har uppfyllts.
* Inträffar när det angivna låne-ID:t inte matchar låne-ID:t på behållaren eller bloben.

## <a name="generate-log-files-for-analysis"></a>Generera loggfiler för analys

I den här självstudien använder vi Message Analyzer för att arbeta med tre olika typer av loggfiler, även om du kan välja att arbeta med någon av dessa:

* **Serverloggen**, som skapas när du aktiverar Azure Storage-loggning. Serverloggen innehåller data om varje åtgärd som anropas mot en av Azure Storage-tjänsterna - blob, kö, tabell och fil. Serverloggen anger vilken åtgärd som anropades och vilken statuskod som returnerades, samt annan information om begäran och svaret.
* **.NET-klientloggen**, som skapas när du aktiverar loggning på klientsidan inifrån .NET-programmet. Klientloggen innehåller detaljerad information om hur klienten förbereder begäran och tar emot och bearbetar svaret.
* **HTTP-nätverksspårningsloggen**, som samlar in data på HTTP/HTTPS-begärande- och svarsdata, inklusive för åtgärder mot Azure Storage. I den här självstudien genererar vi nätverksspårningen via Message Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurera loggning och mått på serversidan

Först måste vi konfigurera Azure Storage-loggning och mått, så att vi har data från tjänstsidan att analysera. Du kan konfigurera loggning och mått på en mängd olika sätt – via [Azure-portalen](https://portal.azure.com), med hjälp av PowerShell eller programmässigt. Se [Aktivera mått](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) och Aktivera [loggning](storage-analytics-logging.md#enable-storage-logging) för information om hur du konfigurerar loggning och mått.

### <a name="configure-net-client-side-logging"></a>Konfigurera LOGGNING på .NET-klientsidan

Om du vill konfigurera loggning på klientsidan för ett .NET-program aktiverar du .NET-diagnostik i programmets konfigurationsfil (web.config eller app.config). Mer information finns [i LOGGNING på klientsidan](https://msdn.microsoft.com/library/azure/dn782839.aspx) på .NET Storage Client Library och [loggning på klientsidan med Microsoft Azure Storage SDK för Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) på MSDN.

Loggen på klientsidan innehåller detaljerad information om hur klienten förbereder begäran och tar emot och bearbetar svaret.

Storage Client Library lagrar loggdata på klientsidan på den plats som anges i programmets konfigurationsfil (web.config eller app.config).

### <a name="collect-a-network-trace"></a>Samla in en nätverksspårning

Du kan använda Message Analyzer för att samla in en HTTP/HTTPS-nätverksspårning medan klientprogrammet körs. Message Analyzer använder [Fiddler](https://www.telerik.com/fiddler) på baksidan. Innan du samlar in nätverksspårningen rekommenderar vi att du konfigurerar Fiddler för att spela in okrypterad HTTPS-trafik:

1. Installera [Fiddler](https://www.telerik.com/download/fiddler).
2. Starta Fiddler.
3. Välj **verktyg | Spelman Alternativ**.
4. Se till att **fånga HTTPS CONNECTs** och **Decrypt HTTPS Traffic** är markerade, som visas nedan.

![Konfigurera fiddler-alternativ](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

För självstudiekursen samlar du in och sparar en nätverksspårning först i Message Analyzer och skapar sedan en analyssession för att analysera spårningen och loggarna. Så här samlar du in en nätverksspårning i Message Analyzer:

1. Välj **Arkiv | Snabbspårning | Okrypterad HTTPS**.
2. Spårningen börjar omedelbart. Välj **Stoppa** om du vill stoppa spårningen så att vi kan konfigurera den så att den bara spårar lagringstrafik.
3. Välj **Redigera** om du vill redigera kalkeringssessionen.
4. Välj länken **Konfigurera** till höger om **Microsoft-Pef-WebProxy** ETW-providern.
5. Klicka på fliken **Provider** i dialogrutan **Avancerade inställningar.**
6. I fältet **Värdnamnsfilter** anger du lagringsslutpunkterna, avgränsade med mellanslag. Du kan till exempel ange dina slutpunkter enligt följande; ändra `storagesample` namnet på ditt lagringskonto:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Avsluta dialogrutan och klicka på **Starta om** för att börja samla in spårningen med värdnamnsfiltret på plats, så att endast Azure Storage-nätverkstrafik ingår i spårningen.

> [!NOTE]
> När du har samlat in nätverksspårningen rekommenderar vi starkt att du återställer de inställningar som du kan ha ändrat i Fiddler för att dekryptera HTTPS-trafik. Avmarkera kryssrutan **Fånga HTTPS CONNECTs** och **Dekryptera HTTPS Traffic** i dialogrutan Spelmansalternativ.

Mer information [finns i Använda nätverksspårningsfunktionerna](https://technet.microsoft.com/library/jj674819.aspx) på Technet.

## <a name="review-metrics-data-in-the-azure-portal"></a>Granska måttdata i Azure-portalen

När ditt program har körts under en viss tid kan du granska måttdiagrammen som visas i [Azure-portalen](https://portal.azure.com) för att observera hur din tjänst har presterat.

Navigera först till ditt lagringskonto i Azure-portalen. Som standard visas ett övervakningsdiagram med måttet **Framgångsprocent** på kontobladet. Om du tidigare har ändrat diagrammet så att det visar olika mått lägger du till måttet **Framgångsprocent.**

Du ser nu **andelen lyckade resultat** i övervakningsdiagrammet, tillsammans med alla andra mått som du kan ha lagt till. I scenariot ska vi undersöka nästa genom att analysera loggarna i Message Analyzer, procent framgång är något under 100%.

Mer information om hur du lägger till och anpassar måttdiagram finns i [Anpassa måttdiagram](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Det kan ta lite tid innan dina måttdata visas i Azure-portalen när du har aktiverat lagringsmått. Detta beror på att timmått för föregående timme inte visas i Azure-portalen förrän den aktuella timmen har förflutit. Dessutom visas inte minutmått för närvarande i Azure-portalen. Så beroende på när du aktiverar mått kan det ta upp till två timmar att se måttdata.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Använda AzCopy för att kopiera serverloggar till en lokal katalog

Azure Storage skriver serverloggdata till blobbar, medan mått skrivs till tabeller. Loggblobar finns i den välkända `$logs` behållaren för ditt lagringskonto. Loggblobar namnges hierarkiskt efter år, månad, dag och timme, så att du enkelt kan hitta det tidsintervall som du vill undersöka. I `storagesample` kontot är `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`till exempel behållaren för loggblobar för 2015-01-02, från 8 till 09.00 . De enskilda blobbar i den här behållaren namnges sekventiellt, med början i `000000.log`.

Du kan använda kommandoradsverktyget AzCopy för att hämta dessa loggfiler på serversidan till valfri plats på den lokala datorn. Du kan till exempel använda följande kommando för att hämta loggfilerna för blob-åtgärder som ägde `C:\Temp\Logs\Server`rum den 2 januari 2015 till mappen . ersätt `<storageaccountname>` med namnet på ditt lagringskonto:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

AzCopy är tillgängligt för hämtning på sidan [Azure Downloads.](https://azure.microsoft.com/downloads/) Mer information om hur du använder AzCopy finns i [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md).

Mer information om hur du hämtar serverloggar finns i [Hämta lagringsloggdata](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Använda Microsoft Message Analyzer för att analysera loggdata

Microsoft Message Analyzer är ett verktyg för att samla in, visa och analysera trafik, händelser och andra system- eller programmeddelanden i felsöknings- och diagnostikscenarier. Med Message Analyzer kan du också läsa in, aggregera och analysera data från logg- och sparade spårningsfiler. Mer information om Message Analyzer finns i [Microsoft Message Analyzer Operating Guide](https://technet.microsoft.com/library/jj649776.aspx).

Message Analyzer innehåller resurser för Azure Storage som hjälper dig att analysera server-, klient- och nätverksloggar. I det här avsnittet kommer vi att diskutera hur du använder dessa verktyg för att hantera problemet med låg procentig framgång i lagringsloggarna.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Hämta och installera Message Analyzer och Azure Storage Assets

1. Hämta [Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) från Microsoft Download Center och kör installationsprogrammet.
2. Starta Meddelandeanalysator.
3. Välj **Resurshanteraren**på **Verktyg-menyn** . Välj **Hämtare**i dialogrutan **Asset Manager** och filtrera sedan på **Azure Storage**. Du kommer att se Azure Storage Assets, som visas i bilden nedan.
4. Klicka på **Synkronisera alla objekt som visas** för att installera Azure Storage Assets. De tillgängliga tillgångarna omfattar:
   * **Färgregler för Azure-lagring:** Med Azure Storage-färgregler kan du definiera särskilda filter som använder färg, text och teckensnitt för att markera meddelanden som innehåller specifik information i en spårning.
   * **Azure Storage-diagram:** Azure Storage-diagram är fördefinierade diagram som diagramserverloggdata. Observera att om du använder Azure Storage-diagram just nu kan du bara läsa in serverloggen i Analysrutnätet.
   * **Azure Storage-delsmakare:** Azure Storage-tolkar tolkar Azure Storage-klienten, servern och HTTP-loggarna för att visa dem i Analysis Grid.
   * **Azure-lagringsfilter:** Azure Storage-filter är fördefinierade villkor som du kan använda för att fråga dina data i Analysrutnätet.
   * **Azure Storage View Layouter:** Azure Storage-vylayouter är fördefinierade kolumnlayouter och grupperingar i analysrutnätet.
5. Starta om Meddelandeanalysatorn när du har installerat tillgångarna.

![Tillgångshanteraren för Meddelandeanalysator](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Installera alla Azure Storage-resurser som visas i den här självstudien.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Importera dina loggfiler till Message Analyzer

Du kan importera alla sparade loggfiler (serversidan, klientsidan och nätverket) till en enda session i Microsoft Message Analyzer för analys.

1. Klicka på **Ny session**på **Arkiv-menyn** i Microsoft Message Analyzer och klicka sedan på **Tom session**. Ange ett namn på analyssessionen i dialogrutan **Ny session.** Klicka på knappen **Filer** på panelen **Sessionsinformation.**
2. Om du vill läsa in nätverksspårningsdata som genereras av Message Analyzer klickar du på **Lägg till filer,** bläddrar till den plats där du sparade TRACpsfilen från webbspårningssessionen, väljer matp-filen och klickar på **Öppna**.
3. Om du vill läsa in loggdata på serversidan klickar du på **Lägg till filer,** bläddrar till den plats där du hämtade loggarna på serversidan, markerar loggfilerna för det tidsintervall du vill analysera och klickar på **Öppna**. Ange sedan listrutan **Textloggkonfiguration** för varje loggfil på serversidan **på** panelen **Sessionsinformation** för att säkerställa att Microsoft Message Analyzer kan tolka loggfilen korrekt.
4. Om du vill läsa in loggdata på klientsidan klickar du på **Lägg till filer,** bläddrar till den plats där du sparade loggarna på klientsidan, markerar de loggfiler som du vill analysera och klickar på **Öppna**. Ange sedan **Session Details** listrutan **Textloggkonfiguration** för varje loggfil på klientsidan på **azureStorageClientDotNetV4** för att säkerställa att Microsoft Message Analyzer kan tolka loggfilen korrekt.
5. Klicka på **Start** i dialogrutan **Ny session** om du vill läsa in och tolka loggdata. Loggdata visas i meddelandeanalysrutnätet.

Bilden nedan visar en exempelsession som konfigurerats med loggfiler för server-, klient- och nätverksspårning.

![Konfigurera meddelandeanalyssession](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Observera att Message Analyzer läser in loggfiler i minnet. Om du har en stor uppsättning loggdata vill du filtrera den för att få bästa prestanda från Message Analyzer.

Bestäm först den tidsram som du är intresserad av att granska och håll den här tidsramen så liten som möjligt. I många fall kommer du vill granska en period av minuter eller timmar som mest. Importera den minsta uppsättningen loggar som kan uppfylla dina behov.

Om du fortfarande har en stor mängd loggdata kanske du vill ange ett sessionsfilter för att filtrera loggdata innan du läser in dem. I rutan **Sessionsfilter** väljer du knappen **Bibliotek** för att välja ett fördefinierat filter. Välj till exempel **Globalt tidsfilter I** från Azure Storage-filtren för att filtrera med ett tidsintervall. Du kan sedan redigera filtervillkoren för att ange start- och sluttidsstämpeln för det intervall som du vill visa. Du kan också filtrera efter en viss statuskod. Du kan till exempel välja att bara läsa in loggposter där statuskoden är 404.

Mer information om hur du importerar loggdata till Microsoft Message Analyzer finns i [Hämta meddelandedata](https://technet.microsoft.com/library/dn772437.aspx) på TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Använd klientbegärans-ID:et för att korrelera loggfilsdata

Azure Storage Client Library genererar automatiskt ett unikt klientbegärande-ID för varje begäran. Det här värdet skrivs till klientloggen, serverloggen och nätverksspårningen, så du kan använda det för att korrelera data över alla tre loggarna i Message Analyzer. Se [klientbegärande-ID](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) för ytterligare information om klientbegäran-ID.

I avsnitten nedan beskrivs hur du använder förkonfigurerade och anpassade layoutvyer för att korrelera och gruppera data baserat på klientbegärande-ID.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Välj en vylayout som ska visas i analysrutnätet

Storage Assets for Message Analyzer innehåller Azure Storage View Layouts, som är förkonfigurerade vyer som du kan använda för att visa dina data med användbara grupperingar och kolumner för olika scenarier. Du kan också skapa anpassade vylayouter och spara dem för återanvändning.

Bilden nedan visar menyn **Visa layout,** tillgänglig genom att välja **Visa layout** i menyfliksområdet i verktygsfältet. Vylayouterna för Azure Storage grupperas under **Azure Storage-noden** på menyn. Du kan `Azure Storage` söka efter i sökrutan för att filtrera endast på Azure Storage-vylayouter. Du kan också välja stjärnan bredvid en vylayout för att göra den till en favorit och visa den högst upp på menyn.

![Menyn Visa layout](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Till att börja med väljer du **Grupperad efter ClientRequestID och Modul**. Den här vylayouten grupperar loggdata från alla tre loggarna först efter klientbegärande-ID och sedan efter källloggfil (eller **Modul** i Meddelandeanalys). Med den här vyn kan du öka detaljnivån i ett visst klientfråre och se data från alla tre loggfilerna för klientbegärande-ID.

Bilden nedan visar den här layoutvyn som tillämpas på exempelloggdata, med en delmängd av kolumnerna. Du kan se att för ett visst klientfrårebegärande-ID visar Analysis Grid data från klientloggen, serverloggen och nätverksspårningen.

![Azure Storage View Layout](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Olika loggfiler har olika kolumner, så när data från flera loggfiler visas i analysrutnätet kanske vissa kolumner inte innehåller några data för en viss rad. I bilden ovan visar klientloggraderna till exempel inga data för kolumnerna **Timestamp**, **TimeElapsed**, **Source**och **Destination,** eftersom dessa kolumner inte finns i klientloggen, men finns i nätverksspårningen. På samma sätt visar kolumnen **Tidsstämpel** tidsstämpel tidsstämpeldata från serverloggen, men inga data visas för kolumnerna **TimeElapsed**, **Source**och **Destination,** som inte ingår i serverloggen.
>
>

Förutom att använda Azure Storage-vylayouter kan du också definiera och spara egna vylayouter. Du kan välja andra önskade fält för att gruppera data och spara grupperingen som en del av din anpassade layout också.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Tillämpa färgregler på analysrutnätet

Lagringstillgångarna innehåller också färgregler som erbjuder ett visuellt sätt att identifiera olika typer av fel i analysrutnätet. De fördefinierade färgreglerna gäller för HTTP-fel, så de visas bara för serverloggen och nätverksspårningen.

Om du vill använda färgregler väljer du **Färgregler** i menyfliksområdet i verktygsfältet. Färgreglerna för Azure Storage visas på menyn. För självstudiekursen väljer du **Klientfel (StatusCode mellan 400 och 499),** som visas på bilden nedan.

![Azure Storage View Layout](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Förutom att använda azure storage-färgreglerna kan du också definiera och spara dina egna färgregler.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Gruppera och filtrera loggdata för att hitta 400-intervallfel

Därefter grupperar och filtrerar vi loggdata för att hitta alla fel i intervallet 400.

1. Leta upp **kolumnen Statuskod** i analysrutnätet, högerklicka på kolumnrubriken och välj **Gruppera**.
2. Därefter grupperar du i kolumnen **ClientRequestId.** Du ser att data i analysrutnätet nu är ordnade efter statuskod och klientbegärande-ID.
3. Visa verktygsfönstret Visa filter om det inte redan visas. Välj **Verktygsfönster**i menyfliksområdet i verktygsfältet och visa sedan **filter**.
4. Om du vill filtrera loggdata så att endast 400 intervallfel visas lägger du till följande filtervillkor i fönstret **Visa filter** och klickar på **Använd:**

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

Bilden nedan visar resultatet av denna gruppering och filtret. Om du expanderar **fältet ClientRequestID** under gruppningen för statuskod 409 visas till exempel en åtgärd som resulterade i den statuskoden.

![Azure Storage View Layout](./media/storage-e2e-troubleshooting/400-range-errors1.png)

När du har använt det här filtret ser du att rader från klientloggen är undantagna, eftersom klientloggen inte innehåller en **StatusCode-kolumn.** Till att börja med granskar vi server- och nätverksspårningsloggarna för att hitta 404 fel, och sedan återgår vi till klientloggen för att undersöka klientåtgärder som ledde till dem.

> [!NOTE]
> Du kan filtrera på **kolumnen StatusKod** och fortfarande visa data från alla tre loggarna, inklusive klientloggen, om du lägger till ett uttryck i filtret som innehåller loggposter där statuskoden är null. Om du vill konstruera det här filteruttrycket använder du:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Det här filtret returnerar alla rader från klientloggen och endast rader från serverloggen och HTTP-loggen där statuskoden är större än 400. Om du använder den på vylayouten grupperad efter klientbegärande-ID och modul kan du söka eller bläddra igenom loggposterna för att hitta de där alla tre loggarna finns representerade.

### <a name="filter-log-data-to-find-404-errors"></a>Filtrera loggdata för att hitta 404 fel

Lagringstillgångarna innehåller fördefinierade filter som du kan använda för att begränsa loggdata för att hitta de fel eller trender du letar efter. Därefter tillämpar vi två fördefinierade filter: ett som filtrerar server- och nätverksspårningsloggarna för 404 fel och en som filtrerar data på ett angivet tidsintervall.

1. Visa verktygsfönstret Visa filter om det inte redan visas. Välj **Verktygsfönster**i menyfliksområdet i verktygsfältet och visa sedan **filter**.
2. I fönstret Visa filter **Library**väljer du `Azure Storage` Bibliotek och söker efter azure storage-filtren. Välj filtret för **404 (hittades inte) meddelanden i alla loggar**.
3. Visa **biblioteksmenyn** igen och leta reda på och välj **det globala tidsfiltret**.
4. Redigera tidsstämplarna som visas i filtret till det område du vill visa. Detta kommer att bidra till att begränsa dataomfånget att analysera.
5. Filtret ska se ut ungefär som exemplet nedan. Klicka på **Använd** om du vill använda filtret i analysrutnätet.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Azure Storage View Layout](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analysera dina loggdata

Nu när du har grupperat och filtrerat dina data kan du undersöka information om enskilda begäranden som genererade 404 fel. I den aktuella vylayouten grupperas data efter klientbegärande-ID och sedan efter loggkälla. Eftersom vi filtrerar på begäranden där fältet StatusCode innehåller 404 ser vi bara server- och nätverksspårningsdata, inte klientloggdata.

Bilden nedan visar en specifik begäran där en Get Blob-åtgärd gav en 404 eftersom bloben inte fanns. Observera att vissa kolumner har tagits bort från standardvyn för att visa relevanta data.

![Filtrerade server- och nätverksspårningsloggar](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Därefter korrelerar vi det här klientbegärande-ID:et med klientloggdata för att se vilka åtgärder klienten utförde när felet inträffade. Du kan visa en ny analysrutnätsvy för den här sessionen om du vill visa klientloggdata som öppnas på en andra flik:

1. Kopiera först värdet för fältet **ClientRequestId** till Urklipp. Du kan göra detta genom att välja endera raden, hitta **fältet ClientRequestId,** högerklicka på datavärdet och välja **Kopiera "ClientRequestId".**
2. Välj **Nytt visningsprogram**i menyfliksområdet i verktygsfältet och välj sedan **Analysrutnät** för att öppna en ny flik. På den nya fliken visas alla data i loggfilerna, utan att gruppera, filtrera eller färga regler.
3. Välj **Visa layout**i menyfliksområdet i verktygsfältet och välj sedan **Alla .NET-klientkolumner** under avsnittet **Azure Storage.** Den här vylayouten visar data från klientloggen samt server- och nätverksspårningsloggarna. Som standard sorteras den i kolumnen **MessageNumber.**
4. Sök sedan i klientloggen efter klientbegärande-ID. Välj Sök meddelanden i menyfliksområdet i **verktygsfältet**och ange sedan ett anpassat filter på klientbegäran-ID:t i fältet **Sök.** Använd den här syntaxen för filtret och ange ditt eget klientfrågefråge-ID:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

Message Analyzer lokaliserar och väljer den första loggposten där sökvillkoren matchar klientbegäran-ID. I klientloggen finns det flera transaktioner för varje klientbegärande-ID, så du kanske vill gruppera dem i fältet **ClientRequestId** för att göra det lättare att se dem alla tillsammans. Bilden nedan visar alla meddelanden i klientloggen för det angivna klientbegärande-ID:et.

![Klientlogg som visar 404 fel](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Med hjälp av data som visas i vylayouterna på dessa två flikar kan du analysera begärandedata för att avgöra vad som kan ha orsakat felet. Du kan också titta på begäranden som föregick den här för att se om en tidigare händelse kan ha lett till 404-felet. Du kan till exempel granska klientloggposterna före det här klientbegärande-ID:t för att avgöra om blobben kan ha tagits bort eller om felet berodde på att klientprogrammet anropar ett CreateIfNotExists API på en behållare eller blob. I klientloggen hittar du blobens adress i fältet **Beskrivning.** I server- och nätverksspårningsloggarna visas den här informationen i fältet **Sammanfattning.**

När du vet adressen till blob som gav 404-felet kan du undersöka vidare. Om du söker i loggposterna efter andra meddelanden som är associerade med åtgärder på samma blob kan du kontrollera om klienten tidigare har tagit bort entiteten.

## <a name="analyze-other-types-of-storage-errors"></a>Analysera andra typer av lagringsfel

Nu när du är bekant med att använda Message Analyzer för att analysera loggdata kan du analysera andra typer av fel med hjälp av vylayouter, färgregler och sökning/filtrering. I tabellerna nedan visas några problem som kan uppstå och de filtervillkor som du kan använda för att hitta dem. Mer information om hur du konstruerar filter och filtret för Meddelandeanalyser finns i [Filtrera meddelandedata](https://technet.microsoft.com/library/jj819365.aspx).

| För att undersöka... | Använd filteruttryck... | Uttrycket gäller för logg (klient, server, nätverk, alla) |
| --- | --- | --- |
| Oväntade förseningar i meddelandeleverans i en kö |AzureStorageClientDotNetV4.Description innehåller "Försök igen misslyckades". |Client |
| HTTP-ökning i PercentThrottlingError |HTTP. Response.StatusCode == 500 &#124;&#124; HTTP. Response.StatusCode == 503 |Nätverk |
| Ökning av ProcentTimeoutError |HTTP. Response.StatusCode == 500 |Nätverk |
| Ökning av PercentTimeoutError (alla) |*StatusCode == 500 |Alla |
| Ökning av PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level < 2 |Client |
| HTTP 403-meddelanden (Förbjudet) |HTTP. Response.StatusCode == 403 |Nätverk |
| HTTP 404-meddelanden (Hittades inte) |HTTP. Response.StatusCode == 404 |Nätverk |
| 404 (alla) |*StatusCode == 404 |Alla |
| Det är problem med SAS-autentiseringen (signatur för delad åtkomst) |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Nätverk |
| HTTP 409-meddelanden (Konflikt) |HTTP. Response.StatusCode == 409 |Nätverk |
| 409 (alla) |*StatusCode == 409 |Alla |
| Låg procentsuccess eller analysloggposter har operationer med transaktionsstatus för ClientOtherErrors |AzureStorageLog.RequestStatus == "ClientOtherError" |Server |
| Nagle Varning |((AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) och (AzureStorageLog.RequestPacketSize <1460) och (AzureStorageLog.EndToEndencyLatMS - AzureStorageLog.ServerLatencyMS >= 200) |Server |
| Tidsintervall i server- och nätverksloggar |#Timestamp >= 2014-10-20T16:36:38 och #Timestamp <= 2014-10-20T16:36:39 |Server, Nätverk |
| Tidsintervall i serverloggar |AzureStorageLog.Timestamp >= 2014-10-20T16:36:38 och AzureStorageLog.Timestamp <= 2014-10-20T16:36:39 |Server |

## <a name="next-steps"></a>Nästa steg

Mer information om felsökning av heltäckande scenarier i Azure Storage finns i följande resurser:

* [Övervaka, diagnostisera och felsök Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Lagringsanalys](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Övervaka ett lagringskonto i Azure-portalen](storage-monitor-storage-account.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Microsoft Message Analyzer – användningsguide](https://technet.microsoft.com/library/jj649776.aspx)
