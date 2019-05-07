---
title: Felsöka Azure Storage med diagnostik- och analysverktyg | Microsoft Docs
description: En självstudie som visar slutpunkt till slutpunkt felsökning med Azure Storage Analytics, AzCopy och Microsoft Message Analyzer
services: storage
author: normesta
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2707081adafa74237e3fb7730837f581e0c8b790
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154228"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Slutpunkt till slutpunkt felsökning med hjälp av Azure Storage-mätvärden och loggning, AzCopy och Message Analyzer
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnostisera och felsöka är en viktig kunskap för att skapa och stöd för program med Microsoft Azure Storage. På grund av distribuerade natur i Azure-program, kan det vara mer komplex än i traditionella miljöer att diagnostisera och felsöka fel och prestandaproblem.

I den här självstudien visar vi hur att identifiera vissa fel som kan påverka prestanda och felsöka dessa fel från slutpunkt till slutpunkt med hjälp av verktyg som tillhandahålls av Microsoft och Azure Storage, för att optimera klientprogrammet.

Den här självstudien innehåller en praktisk utforskning av ett felsökning slutpunkt till slutpunkt-scenario. Utförliga konceptuella instruktioner för att felsöka Azure storage-program, se [övervaka, diagnostisera och Felsök Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Verktyg för felsökning av Azure Storage-program
Du kan använda en kombination av verktyg för att avgöra när ett problem har uppstått och vad orsaken till problemet kan uppstå om du vill felsöka klientprogram som använder Microsoft Azure Storage. Dessa verktyg innefattar:

* **Azure Storage Analytics**. [Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) innehåller mått och loggning för Azure Storage.

  * **Lagringsmått** spårar transaktionsmått och kapacitet för ditt lagringskonto. Med hjälp av mätvärden, kan du bestämma hur programmet fungerar enligt en mängd olika mått. Se [Schema över Måttabeller i Storage Analytics](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) för mer information om vilka typer av mått som spåras av Storage Analytics.
  * **Med loggningen i Storage** loggar varje begäran till Azure Storage-tjänster till en logg för serversidan. Loggen spårar detaljerade data för varje begäran, inklusive de åtgärder som utförs, status för åtgärden och latensinformation. Se [Storage Analytics loggformat](/rest/api/storageservices/Storage-Analytics-Log-Format) för mer information om begäranden och svar-data som skrivs till loggarna av Storage Analytics.

* **Azure-portalen**. Du kan konfigurera mått och loggning för ditt lagringskonto i den [Azure-portalen](https://portal.azure.com). Du kan också visa diagram som visar hur programmet fungerar med tiden och konfigurerar aviseringar som meddelar dig om ditt program fungerar annorlunda än förväntat för ett visst mått.

    Se [övervaka ett lagringskonto i Azure-portalen](storage-monitor-storage-account.md) information om hur du konfigurerar övervakning i Azure-portalen.
* **AzCopy**. Serverloggar för Azure Storage lagras som BLOB-objekt, så du kan använda AzCopy för att kopiera loggblobarna till en lokal katalog för analys med hjälp av Microsoft Message Analyzer. Se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md) för mer information om AzCopy.
* **Microsoft Message Analyzer**. Message Analyzer är ett verktyg som förbrukar loggfiler och visar loggdata i ett visuellt format som gör det enkelt att filter, search och gruppen loggdata till användbara uppsättningar som du kan använda för att analysera fel och prestandaproblem. Se [fungerar handboken för Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx) för mer information om analysverktyg.

## <a name="about-the-sample-scenario"></a>Om Exempelscenario
Den här självstudien tittar vi närmare på ett scenario där Azure Storage-mätvärden anger en låg procent Slutförandefrekvens för ett program som anropar Azure storage. Låg procent lyckade rate mått (visas som **PercentSuccess** i den [Azure-portalen](https://portal.azure.com) och tabeller för mått) spårar åtgärder som lyckas, men som returnerar en HTTP-statuskod som är större än 299. I loggfilerna från serversidan storage de här åtgärderna registreras med transaktionsstatus **ClientOtherErrors**. Mer information om låg procent lyckade mått finns i [mätningar visar låga PercentSuccess eller analytics loggposter har åtgärder med transaktionsstatus av ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure Storage-åtgärder kan returnera HTTP-statuskoder är större än 299 som en del av deras normala funktioner. Men dessa fel i vissa fall kan indikera att du kan optimera ditt klientprogram för bättre prestanda.

I detta scenario vi att överväga en låg procent Slutförandefrekvens vara något under 100%. Du kan välja en annan mått nivå, men efter dina behov. Vi rekommenderar att under testningen av ditt program du upprätta en baslinje tolerans för dina viktiga prestandamått. Exempel: du kan fastställa baserat på testning, att ditt program bör ha en konsekvent procent frekvensen av 90% eller 85%. Om dina måttdata visar att programmet avviker från det numret, så du kan undersöka vilka kan orsaka ökningen.

I vår Exempelscenario när vi har visat att procent lyckade rate mått är mindre än 100%, ska vi granska loggarna för att hitta de fel som stämmer med måtten och använda dem för att ta reda på vad som orsakar lägre procent frekvensen. Vi ska titta närmare på fel i 400-intervallet. Sedan kommer vi närmare undersöka 404 (hittades inte)-fel.

### <a name="some-causes-of-400-range-errors"></a>Vissa orsakerna till 400-intervallet fel
I exemplen nedan visas ett urval av 400-intervallet fel för begäranden mot Azure Blob Storage och deras möjliga orsaker. Någon av dessa fel, samt fel i intervallet 300 och 500 intervallet kan bidra till en låg procent Slutförandefrekvens.

Observera att listorna nedan är långt från klar. Se [Status och felkoder](https://msdn.microsoft.com/library/azure/dd179382.aspx) på MSDN för information om Allmänt Azure Storage-fel och om fel som är specifika för varje lagringstjänst.

**Status 404 (hittades inte)-kodexempel**

Inträffar när en Läsåtgärd mot en behållare eller blob misslyckas eftersom bloben eller behållaren inte kan hittas.

* Inträffar om en behållare eller blob har tagits bort av en annan klient innan den här begäran.
* Inträffar om du använder ett API-anrop som skapar behållare eller blob när du har kontrollerat om den finns. The CreateIfNotExists APIs ringa HEAD först för att kontrollera om finns av behållare eller blob; Om det inte finns ett 404-fel returneras och sedan ett andra PUT-anrop görs för att skriva behållare eller blob.

**Statuskod 409 (konflikt)-exempel**

* Uppstår om du använder en skapa-API för att skapa en ny behållare eller blob, utan att först kontrollera om finns och en behållare eller blob med det namnet finns redan.
* Inträffar om en behållare tas bort och du försöker skapa en ny behållare med samma namn innan borttagningen är klar.
* Uppstår om du anger ett lån på en behållare eller blob och det finns redan ett lån finns.

**Statuskod 412 (Förhandsvillkoret misslyckades) exempel**

* Inträffar när de villkor som anges av en villkorlig rubrik inte har uppfyllts.
* Inträffar när lån-ID som angetts inte matchar lån-ID på behållare eller blob.

## <a name="generate-log-files-for-analysis"></a>Skapa loggfiler för analys
I den här självstudien använder vi Message Analyzer för att arbeta med tre olika typer av loggfiler, men du kan välja att arbeta med någon av dessa:

* Den **serverloggen**, som skapas när du aktiverar loggning för Azure Storage. Serverloggen innehåller information om varje åtgärd som kallas för en Azure Storage-tjänster – blob, kö, tabell och fil. Serverloggen anger vilken åtgärd anropades och vilken statuskod var returnerade, samt annan information om begäran och svaret.
* Den **.NET-klientloggen**, som skapas när du aktiverar loggning på klientsidan från inom ditt .NET-program. Klientloggen innehåller detaljerad information om hur klienten förbereder begäran och tar emot och bearbetar svaret.
* Den **HTTP nätverk spårningsloggen**, som samlar in data för HTTP/HTTPS-begäran och svaret data, inklusive för åtgärder mot Azure Storage. Vi ska generera nätverksspårning via Message Analyzer i de här självstudierna.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurera serversidan loggning och mått
Först måste du konfigurera Azure Storage-loggning och mått, så att vi har data från serversidan att analysera. Du kan konfigurera loggning och mått i en mängd olika sätt – den [Azure-portalen](https://portal.azure.com), med hjälp av PowerShell, eller programmässigt. Se [aktivera mätvärden](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) och [aktivera loggning](storage-analytics-logging.md#enable-storage-logging) mer information om hur du konfigurerar loggning och mått.

### <a name="configure-net-client-side-logging"></a>Konfigurera loggning för klientsidan av .NET
Aktivera .NET diagnostik i programmets konfigurationsfil (web.config eller app.config) om du vill konfigurera loggning på klientsidan för en .NET-program. Se [Klientloggning med .NET Storage Client Library](https://msdn.microsoft.com/library/azure/dn782839.aspx) och [Klientloggning med Microsoft Azure Storage SDK för Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) på MSDN för mer information.

Klientsidan loggen innehåller detaljerad information om hur klienten förbereder begäran och tar emot och bearbetar svaret.

Storage-klientbiblioteket lagrar klientsidan loggdata i den plats som anges i programmets konfigurationsfil (web.config eller app.config).

### <a name="collect-a-network-trace"></a>Samla in en nätverksspårning
Du kan använda Message Analyzer för att samla in en HTTP/HTTPS-nätverksspårning medan klientprogrammet är igång. Message Analyzer använder [Fiddler](https://www.telerik.com/fiddler) på serverdelen. Innan du samlar in nätverksspårningen, rekommenderar vi att du konfigurerar Fiddler för att registrera okrypterade HTTPS-trafik:

1. Installera [Fiddler](https://www.telerik.com/download/fiddler).
2. Starta Fiddler.
3. Välj **verktyg | Fiddler alternativ**.
4. I dialogrutan Alternativ kontrollerar du att **avbilda HTTPS ansluter** och **dekryptera HTTPS-trafik** båda är valda, enligt nedan.

![Konfigurera alternativ för Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Samla in och spara en nätverksspårning först i Message Analyzer och skapa en analysis-session för att analysera spårningen och loggar för den här självstudien. Samla in en nätverksspårning i Message Analyzer:

1. Välj i Message Analyzer **fil | Snabb spårning | Okrypterade HTTPS**.
2. Spårningen träder i kraft omedelbart. Välj **stoppa** att stoppa spårningen så att vi kan konfigurera för att spåra lagringsrelaterad trafik.
3. Välj **redigera** redigera spårningssessionen.
4. Välj den **konfigurera** länken till höger om den **Microsoft – Pef-WebProxy** ETW-provider.
5. I den **avancerade inställningar** dialogrutan klickar du på den **Provider** fliken.
6. I den **värdnamn Filter** fältet, anger du ditt storage-slutpunkter, avgränsade med blanksteg. Exempel: du kan ange dina slutpunkter enligt följande; ändra `storagesample` till namnet på ditt lagringskonto:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Stänga dialogrutan och klicka på **starta om** att börja samla in spårningen med värdnamn filtret på plats, så att endast Azure Storage-nätverkstrafik ingår i spårningen.

> [!NOTE]
> När du är klar med att samla in dina nätverksspårning rekommenderar vi starkt att du återställer de inställningar som du har ändrat i Fiddler att dekryptera HTTPS-trafik. I dialogrutan Fiddler alternativ avmarkera den **avbilda HTTPS ansluter** och **dekryptera HTTPS-trafik** kryssrutorna.
>
>

Se [med hjälp av spårning av nätverksfunktioner](https://technet.microsoft.com/library/jj674819.aspx) på Technet för mer information.

## <a name="review-metrics-data-in-the-azure-portal"></a>Granska mätvärden i Azure portal
När ditt program har körts i en viss tidsperiod, kan du granska måttdiagram som visas i den [Azure-portalen](https://portal.azure.com) att Observera hur tjänsten fungerar.

Navigera först till ditt lagringskonto i Azure-portalen. Som standard en övervakning diagram med de **slutförandeprocent** mått visas på bladet för kontot. Om du tidigare har ändrat diagram för att visa olika mått, lägger du till den **slutförandeprocent** mått.

Nu ser du **slutförandeprocent** i Övervakningsdiagrammet, tillsammans med andra mått du kanske har lagt till. Vi ska undersöka bredvid genom att analysera loggar i Message Analyzer för scenariot är procent frekvensen något under 100%.

Mer information om att lägga till och anpassa måttdiagram finns i [anpassa måttdiagram](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Det kan ta lite tid innan din måttdata visas i Azure-portalen när du har aktiverat lagringsmått. Det beror på att varje timme mått för föregående timma inte visas i Azure portal tills den aktuella timman har gått ut. Dessutom visas minutmått för närvarande inte i Azure-portalen. Därför beroende på när du aktiverar mått, det kan ta upp till två timmar att visa mätvärden.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Använda AzCopy för att kopiera loggar till en lokal katalog
Azure Storage skriver server loggdata för blobar, medan mätvärdena skrivs till tabeller. Loggblobarna är tillgängliga i den välkända `$logs` behållare för ditt lagringskonto. Loggblobarna namnges hierarkiskt per år, månad, dag och timme, så att du lätt kan hitta tidsperiod som du vill undersöka. Till exempel i den `storagesample` konto, behållare för loggblobarna för 01/02/2015 från 8 – 9 am, är `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Enskilda blobbar i den här behållaren namnges sekventiellt, från och med `000000.log`.

Du kan använda kommandoradsverktyget AzCopy för att ladda ned dessa från serversidan loggfiler till en valfri plats på den lokala datorn. Du kan till exempel använda följande kommando för att ladda ned filerna för blobåtgärder som utfördes på den 2 januari 2015 till mappen `C:\Temp\Logs\Server`; Ersätt `<storageaccountname>` med namnet på ditt lagringskonto och `<storageaccountkey>` med din åtkomstnyckel :

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy är tillgänglig för hämtning på den [Azure hämtar](https://azure.microsoft.com/downloads/) sidan. Mer information om hur du använder AzCopy finns i [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md).

Mer information om hur du hämtar loggarna för serversidan finns [ladda ned Storage Logging loggdata](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Använd Microsoft Message Analyzer för att analysera loggdata
Microsoft Message Analyzer är ett verktyg för att samla in, visa och analysera protokollet messaging trafik, händelser och andra system- eller meddelanden i scenarier för felsökning och diagnostik. Analysverktyg också kan du läsa in, sammanställa och analysera data från loggen och sparade spårningsfiler. Läs mer om analysverktyg, [fungerar handboken för Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

Message Analyzer innehåller tillgångar för Azure Storage som hjälper dig att analysera servern, klienten och nätverksloggar. I det här avsnittet diskuterar vi hur du använder dessa verktyg för att åtgärda problemet för en låg procent i storage-loggar.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Hämta och installera Message Analyzer och Azure Storage-tillgångar
1. Ladda ned [Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) från Microsoft Download Center och kör installationsprogrammet.
2. Starta Message Analyzer.
3. Från den **verktyg** menyn och välj **Tillgångsansvarig**. I den **Tillgångsansvarig** dialogrutan **hämtar**, sedan kan filtrera efter **Azure Storage**. Azure Storage-tillgångar visas, enligt bilden nedan.
4. Klicka på **synkronisera alla visas objekt** att installera Azure Storage-tillgångar. De tillgängliga resurserna är:
   * **Regler för Azure Storage-färg:** Azure Storage-färgregler kan du definiera särskilda filter med färg, text och inställningar för teckensnitt som används för att markera meddelanden som innehåller specifik information i en spårning.
   * **Azure Storage-diagram:** Azure Storage-diagram är fördefinierade diagram som graph server loggdata. Observera att om du vill använda Azure Storage-diagram för tillfället, du kan bara läser in serverloggen i rutnätet analys.
   * **Azure Storage-Parser:** Azure Storage-Parser parsa Azure Storage-klienten, server och HTTP-loggar för att visa dem i rutnätet för analys.
   * **Azure Storage-filter:** Azure Storage-filter är fördefinierade villkor som du kan använda för att fråga data i rutnätet för analys.
   * **Azure Storage layouter:** Azure Storage-vylayouter är fördefinierad kolumn och grupperingar i rutnätet för analys.
5. Starta om analysverktyg när du har installerat tillgångarna.

![Message Analyzer Tillgångshanteraren](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Installera alla Azure Storage-resurser som visas för den här självstudiekursen.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Importera loggfilerna till Message Analyzer
Du kan importera alla dina sparade loggfiler (serversidan, på klientsidan och nätverk) till en enda session i Microsoft Message Analyzer för analys.

1. På den **filen** klickar du på menyn i Microsoft Message Analyzer **ny Session**, och klicka sedan på **tom Session**. I den **ny Session** dialogrutan, ange ett namn för din analys-session. I den **sessionsinformation** panelen, klickar du på den **filer** knappen.
2. Om du vill läsa in nätverk spårningsdata som genereras av Message Analyzer, klickar du på **Lägg till filer**, bläddra till platsen där du sparade filen .matp från dina webb-spårningssessionen, markera filen .matp och klicka på **öppna**.
3. Om du vill läsa in loggdata för serversidan, klickar du på **Lägg till filer**, bläddra till den plats där du sparade loggarna serversidan, väljer du loggfilerna för tidsintervall som du vill analysera klicka sedan på **öppna**. I den **sessionsinformation** panelen genom att ange den **Text Log Configuration** listrutan för varje serversidan loggfilen till **AzureStorageLog** så att Microsoft Message Analyzer kan parsa filen korrekt.
4. Om du vill läsa in loggdata för klientsidan, klickar du på **Lägg till filer**, bläddra till den plats där du sparade loggarna på klientsidan, välja loggfiler som du vill analysera klicka sedan på **öppna**. I den **sessionsinformation** panelen genom att ange den **Text Log Configuration** listrutan för varje klientsidan loggfilen till **AzureStorageClientDotNetV4** att se till att Microsoft Message Analyzer kan parsa filen korrekt.
5. Klicka på **starta** i den **ny Session** dialogrutan för att läsa in och tolka loggdata. Loggdata som visas i rutnätet Message Analyzer analys.

I bilden nedan visas en exempel-session som har konfigurerats med servern, klienten och spårningsloggfilerna i nätverket.

![Konfigurera Message Analyzer Session](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Observera att Message Analyzer läser in loggfiler i minnet. Om du har ett stort antal loggdata, kommer du att filtrera för att få bästa möjliga prestanda från analysverktyg.

Först bestämmer du tidsramen som du vill granska och hålla det här tidsintervallet så mycket som möjligt. I många fall kommer du vill granska en viss minuter eller timmar högst. Importera den minsta mängden loggar som uppfyller dina behov.

Om du fortfarande har en stor del av loggdata kan sedan du ange en session och filtrerar loggdata innan du läser in den. I den **Session Filter** väljer den **biblioteket** knappen om du vill välja ett fördefinierat filter, till exempel välja **Global tid Filter I** från Azure Storage-filter på Filtrera på ett tidsintervall. Du kan sedan redigera filtervillkoren om du vill ange de inledande och avslutande tidsstämpel för intervallet som du vill se. Du kan också filtrera på en viss status-kod. Du kan till exempel välja att läsa in bara poster där statuskoden är 404.

Läs mer om att importera loggdata till Microsoft Message Analyzer [hämtar meddelandedata](https://technet.microsoft.com/library/dn772437.aspx) på TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Använd klientbegärans-ID för att korrelera data för loggfil
Azure Storage-klientbiblioteket genererar automatiskt ett unikt klient-ID för begäran för varje begäran. Det här värdet skrivs till klientloggen och serverloggen nätverksspårning, så att du kan använda den för att korrelera data över alla tre loggar inom Message Analyzer. Se [ID för klientbegäran](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) för ytterligare information om klienten begär ID.

I avsnitten nedan beskrivs hur du använder layout förkonfigurerade och anpassade vyer för att korrelera och gruppera data baserat på klienten begärande-ID.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Välj en vy-layout ska visas i rutnätet för analys
Storage-material för Message Analyzer inkluderar Azure Storage layouter, som är förkonfigurerade vyer som du kan använda för att visa dina data med användbara grupperingar och kolumner för olika scenarier. Du kan också skapa anpassade layouter och spara dem för återanvändning.

Bilden nedan visar den **visa Layout** menyn genom att välja **visa Layout** menyflikarna verktygsfältet. Vylayouter för Azure Storage är grupperade under den **Azure Storage** nod i menyn. Du kan söka efter `Azure Storage` Visa endast layouter i sökrutan för att filtrera på Azure Storage. Du kan också välja stjärnan bredvid en vy-layout blir en favorit och visa dem längst ned på menyn.

![Visa-menyn för Layout](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Välj börjar med **grupperade efter ClientRequestID och modulen**. Den här vyn layout grupper logga data från alla tre loggar först efter ID för klientbegäran, sedan efter källa loggfilen (eller **modulen** i Message Analyzer). Med den här vyn kan du granska nedåt i en viss klientbegäran-ID och visa data från alla tre loggfilerna för att klientbegäran-ID.

Bilden nedan visar den här layoutvyn tillämpas på log exempeldata, med en delmängd med kolumner som visas. Du kan se att Analysis rutnätet för en viss för Klientförfrågan visar data från den klientloggen och serverloggen nätverksspårning.

![Azure Storage visa Layout](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Olika loggfilerna har olika kolumner, så när data från flera loggfiler visas i rutnätet för analys, vissa kolumner inte får innehålla några data för en viss rad. Till exempel i bilden ovan klienten log rader visas inte några data för den **tidsstämpel**, **TimeElapsed**, **källa**, och **mål**kolumner, eftersom dessa kolumner som inte finns i klientloggen för, men finns i nätverksspårningen. På samma sätt kan den **tidsstämpel** kolumnen visar tidsstämpel data från serverloggen, men inga data visas för den **TimeElapsed**, **källa**, och  **Mål** kolumner som inte är en del av serverloggen.
>
>

Förutom att använda Azure Storage-vylayouter kan du också definiera och spara egna layouter. Du kan välja andra önskade fält för gruppering av data och spara grupperingen som en del av din anpassade layout.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Avser Analysis rutnätet färgregler
Storage-material inkluderar också färgregler som ger ett visuellt objekt innebär att identifiera olika typer av fel i rutnätet för analys. Fördefinierade färgregler gäller för HTTP-fel, så att de visas endast för serverspårning logg- och nätverk.

Markera för att tillämpa färgregler **färgregler** menyflikarna verktygsfältet. Azure Storage-färgregler på menyn visas. För den här självstudien väljer **klientfel (StatusCode mellan 400 och 499)**, enligt bilden nedan.

![Azure Storage visa Layout](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Förutom att använda Azure Storage-färgregler kan du också definiera och spara egna färgregler.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Gruppen och filtrera loggdata för att hitta 400-intervallet fel
Nu ska vi gruppen och filtrera loggdata för att hitta alla fel i 400-intervallet.

1. Leta upp den **StatusCode** kolumn i rutnätet för analys, högerklicka på kolumnen rubrik- och väljer **grupp**.
2. Gruppera sedan efter den **ClientRequestId** kolumn. Ser du att data i rutnätet Analysis nu ordnas genom statuskoden och klienten begärande-ID.
3. Visa fönstret Visa Filter verktyget om den inte redan visas. I verktygsfältet i menyfliksområdet väljer **verktyget Windows**, sedan **vyfilter**.
4. Om du vill filtrera loggdata för att visa endast 400-intervallet fel, lägger du till följande filter kriterier för de **vyfilter** , och klicka på **tillämpa**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

I bilden nedan visas resultatet av den här grupperingen och filter. Expandera den **ClientRequestID** fältet under gruppering för statuskod 409, till exempel visar en åtgärd som resulterade i att statuskod.

![Azure Storage visa Layout](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Efter att ha tillämpat det här filtret, ser du att rader från klientloggen undantas, som klienten loggen inte innehåller en **StatusCode** kolumn. Börja med att vi ska gå igenom den servern och nätverket spårningsloggar för att hitta 404-fel och sedan vi återkommer till klientloggen att undersöka Klientåtgärder som ledde till dem.

> [!NOTE]
> Du kan filtrera efter den **StatusCode** kolumn och fortfarande visa data från alla tre loggar, inklusive klientloggen om du lägger till ett uttryck filter som innehåller poster där statuskoden är null. För att skapa den här filteruttrycket, använder du:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Det här filtret returnerar alla rader från klienten logg- och endast rader från serverloggen och HTTP-log där statuskoden är större än 400. Om du använder för att visa layouten grupperade efter ID för klientbegäran och modulen, du kan söka eller bläddra igenom vilka loggposter du hittar sådana där alla tre loggar visas.   
>
>

### <a name="filter-log-data-to-find-404-errors"></a>Filtrera loggdata för att hitta 404-fel
Storage-material inkluderar fördefinierade filter som du kan använda för att begränsa loggdata för att hitta fel eller trender som du letar efter. Nu ska vi ska använda två fördefinierade filter: en som filtrerar servern och nätverket spårningsloggar för 404-fel och en som filtrerar data på ett angivet tidsintervall.

1. Visa fönstret Visa Filter verktyget om den inte redan visas. I verktygsfältet i menyfliksområdet väljer **verktyget Windows**, sedan **vyfilter**.
2. I fönstret Visa Filter väljer **biblioteket**, och Sök på `Azure Storage` hitta Azure Storage-filter. Välj filter för **404 (hittades inte) meddelanden i alla loggar**.
3. Visa den **biblioteket** menyn igen, och leta upp och välj den **globala tidsfilter**.
4. Redigera tidsstämplar som visas i filtret för att det intervall som du vill visa. Detta hjälper för att begränsa vilka data som ska analyseras.
5. Filtret bör likna exemplet nedan. Klicka på **tillämpa** att tillämpa filtret i rutnätet för analys.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Azure Storage visa Layout](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analysera dina loggdata
Nu när du har grupperade och filtrerade dina data, kan du granska information om enskilda förfrågningar som genererat 404-fel. I den aktuella vyn layouten grupperas data efter ID för klientbegäran, sedan efter logg källa. Eftersom vi filtrerar på begäranden där fältet StatusCode innehåller 404 kan se vi endast server och spårningsdata för nätverket, inte klienten loggdata.

I bilden nedan visas en specifik begäran där en hämta Blob-åtgärden gav ett 404 eftersom blobben inte finns. Observera att vissa kolumner har tagits bort från standardvyn för att visa relevanta data.

![Filtrerade servern och nätverket spårningsloggar](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Vi kommer sedan att göra detta klient-ID för begäran med loggdata för klienten att se vilka åtgärder som klienten tog när felet inträffade. Du kan visa en ny Analysis rutnätsvy för den här sessionen att visa loggen klientdata, som öppnas i en andra fliken:

1. Först kopiera värdet för den **ClientRequestId** fältet till Urklipp. Du kan göra detta genom att välja antingen rad, lokalisera den **ClientRequestId** fält, högerklicka på datavärdet och välja **kopia 'ClientRequestId'**.
2. I verktygsfältet i menyfliksområdet väljer **nya Viewer**och välj sedan **Analysis Grid** att öppna en ny flik. Den nya fliken visar alla data i loggfilerna, utan att gruppera, filtrera eller färgregler.
3. I verktygsfältet i menyfliksområdet väljer **visa Layout**och välj sedan **alla kolumner i .NET-klient** under den **Azure Storage** avsnittet. Layout för den här vyn visar data från klienten log samt de server- och spårningsloggarna. Som standard den sorteras på den **MessageNumber** kolumn.
4. Nu söka klientloggen för klienten begärande-ID. I verktygsfältet i menyfliksområdet väljer **hitta meddelanden**, ange ett filter på klient-ID för begäran i den **hitta** fält. Använd den här syntaxen för filtret, ange din egen klientbegärans-ID:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Message Analyzer söker efter och väljer den första loggposten där sökvillkoret matchar klienten begärande-ID. I klientloggen, det finns flera poster för varje klientbegärans-ID, så kan du gruppera dem på den **ClientRequestId** fält som du vill göra det enklare att se dem allt på samma plats. I bilden nedan visas alla meddelanden i klientloggen för för den angivna klienten begärande-ID.

![Klienten log som visar 404-fel](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Du kan med hjälp av data som visas i vylayouter i dessa två flikar, för att analysera data för programbegäranden för att fastställa vad kan ha orsakat felet. Du kan också titta på begäranden som föregås aktiviteten för att se om en föregående händelse kan lett till 404-fel. Du kan till exempel granska loggposterna klienten före detta klient-ID för begäran om att avgöra om blob kan ha tagits bort eller om felet beror på klientprogram som anropar ett CreateIfNotExists API på en behållare eller blob. I klientloggen, hittar du den blob-adress i den **beskrivning** fältet; på den servern och nätverket spårningsloggar den här informationen visas i den **sammanfattning** fält.

När du känner till adressen för bloben som gav 404-fel kan undersöka du vidare. Om du söker loggposter för andra meddelanden som är associerade med åtgärder på samma blob kan kontrollera du om klienten bort entiteten.

## <a name="analyze-other-types-of-storage-errors"></a>Analysera andra typer av lagringsfel
Nu när du är van vid att använda Message Analyzer för att analysera loggdata, du kan analysera andra typer av fel med hjälp av vyn layouter färgregler och söka /-filtrering. I tabellerna nedan visas några problem som kan uppstå och filtervillkoren som du kan använda för att hitta dem. Mer information om att konstruera filter och analysverktyg som filtrering språk finns i [filtrering meddelandedata](https://technet.microsoft.com/library/jj819365.aspx).

| Att undersöka... | Använd filteruttrycket... | Uttrycket som gäller för Log (klient, Server, nätverk, alla) |
| --- | --- | --- |
| Oväntade fördröjningar i en köad meddelandeleverans |AzureStorageClientDotNetV4.Description innehåller ”försöker igen misslyckades åtgärden”. |Client |
| HTTP-ökning i PercentThrottlingError |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |Nätverk |
| Öka i PercentTimeoutError |HTTP.Response.StatusCode   == 500 |Nätverk |
| Öka i PercentTimeoutError (alla) |*StatusCode   == 500 |Alla |
| Öka i PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Client |
| HTTP 403 (förbjudet) meddelanden |HTTP.Response.StatusCode   == 403 |Nätverk |
| HTTP 404 (hittades inte) meddelanden |HTTP.Response.StatusCode   == 404 |Nätverk |
| 404 (alla) |*StatusCode   == 404 |Alla |
| Delad åtkomst (Signatur) auktorisering problem |AzureStorageLog.RequestStatus ==  "SASAuthorizationError" |Nätverk |
| HTTP 409 (konflikt) meddelanden |HTTP. Response.StatusCode == 409 |Nätverk |
| 409 (alla) |*StatusCode   == 409 |Alla |
| Låg PercentSuccess eller analytics loggposter har åtgärder med transaktionsstatus av ClientOtherErrors |AzureStorageLog.RequestStatus ==   "ClientOtherError" |Server |
| Nagle varning |((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) och (AzureStorageLog.RequestPacketSize < 1 460) och (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200) |Server |
| Tidsintervall i Server- och loggar |#Timestamp > = 2014-10-20T16:36:38 och #Timestamp < = 2014-10-20T16:36:39 |Server, nätverk |
| Tidsintervall i serverloggar |AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 och AzureStorageLog.Timestamp < = 2014-10-20T16:36:39 |Server |

## <a name="next-steps"></a>Nästa steg
Mer information om felsökning slutpunkt till slutpunkt-scenarier i Azure Storage finns i följande resurser:

* [Övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Lagringsanalys](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Övervaka ett lagringskonto i Azure portal](storage-monitor-storage-account.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Microsoft Message Analyzer fungerar Guide](https://technet.microsoft.com/library/jj649776.aspx)
