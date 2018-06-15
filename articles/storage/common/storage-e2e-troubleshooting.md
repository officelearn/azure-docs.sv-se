---
title: Felsöka Azure Storage med diagnostik & Message Analyzer | Microsoft Docs
description: En självstudiekurs visar slutpunkt till slutpunkt felsökning med Azure Storage Analytics, AzCopy och Microsoft Message Analyzer
services: storage
documentationcenter: dotnet
author: tamram
manager: timlt
ms.assetid: 643372a3-1c07-4e88-b4ef-042512a43086
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.openlocfilehash: 324370ae18627a1985e6a40aec11ee2fa871e93b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30323312"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Slutpunkt till slutpunkt felsökning med hjälp av Azure Storage-mätvärden och loggning, AzCopy och Message Analyzer
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnostisera och felsöka är en nyckel kunskap för att bygga och stöd för program med Microsoft Azure Storage. På grund av distribuerade uppbyggnad ett Azure-program, kan det vara mer komplexa än i vanliga miljöer att diagnostisera och felsöka fel och problem med prestanda.

I den här kursen visar vi hur att identifiera vissa fel som kan påverka prestanda och felsökning av dessa fel från slutpunkt till slutpunkt med hjälp av verktyg som tillhandahålls av Microsoft och Azure Storage för att optimera klientprogrammet.

Den här självstudiekursen innehåller en praktisk utforska för ett scenario för slutpunkt till slutpunkt-felsökning. En detaljerad konceptuella guide till felsökning av Azure storage-program finns [övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Verktyg för felsökning av Azure Storage-program
Du kan använda en kombination av verktyg för att felsöka klientprogram som använder Microsoft Azure Storage för att bestämma när ett problem har uppstått och vad som kan vara orsaken till problemet. Dessa verktyg innefattar:

* **Azure Storage Analytics**. [Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) innehåller mått och loggning för Azure Storage.
  
  * **Storage-mätvärden** spårar transaktion mått och kapacitetsmått för ditt lagringskonto. Med hjälp av mätvärden, kan du bestämma hur programmet fungerar enligt en mängd olika åtgärder. Se [Storage Analytics mätvärden tabellschemat](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) för mer information om vilka typer av mått som spåras av Storage Analytics.
  * **Lagring loggning** loggar varje begäran i Azure Storage-tjänster till en logg för serversidan. Loggen spårar detaljerad information för varje begäran, inklusive åtgärd utförs, status för drift och latensinformation. Se [Storage Analytics loggformatet](/rest/api/storageservices/Storage-Analytics-Log-Format) mer information om begärande- och data som skrivs till loggarna av Storage Analytics.

* **Azure-portalen**. Du kan konfigurera mått och loggning för ditt lagringskonto i den [Azure-portalen](https://portal.azure.com). Du kan också visa tabeller och diagram som visar hur programmet fungerar över tid och konfigurera varningar som meddelar dig om ditt program fungerar annorlunda än förväntat för ett visst mått.
  
    Se [övervaka ett lagringskonto i Azure portal](storage-monitor-storage-account.md) information om hur du konfigurerar övervakning i Azure-portalen.
* **AzCopy**. Server-loggar för Azure Storage lagras som blobbar, så du kan använda AzCopy för att kopiera loggen BLOB till en lokal katalog för analys med hjälp av Microsoft Message Analyzer. Se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md) för mer information om AzCopy.
* **Microsoft Message Analyzer**. Message Analyzer är ett verktyg som förbrukar loggfiler och visar loggdata i ett visuellt format som gör det enkelt att filtrera, söka efter och grupp loggdata i användbara grupper som du kan använda för att analysera fel och problem med prestanda. Se [operativsystem handboken för Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) mer information om Message Analyzer.

## <a name="about-the-sample-scenario"></a>Om exempelscenariot
Vi går igenom ett scenario där Azure Storage-mätvärden anger en låg procent slutförandenivå för ett program som anropar Azure-lagring för den här självstudiekursen. Låg procent lyckade hastighet mått (visas som **PercentSuccess** i den [Azure-portalen](https://portal.azure.com) och mått tabeller) spårar åtgärder som lyckas, men som kan returnera ett HTTP-statuskod som är större än 299. I loggfilerna serversidan lagring åtgärderna registreras med transaktionsstatus **ClientOtherErrors**. Mer information om låg procent lyckade mått finns [mätvärdena visar låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus av ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure Storage-åtgärder kan returnera HTTP-statuskoder större än 299 som en del av deras normala funktioner. Men dessa fel i vissa fall kan indikera att du kanske att optimera ditt klientprogram för bättre prestanda.

I det här scenariot ska vi anser att en låg procent slutförandenivå vara allt under 100%. Du kan välja en annan mått nivå, men efter dina behov. Vi rekommenderar att under testningen av programmet, du upprätta en baslinje tolerans för dina nyckeltal. T.ex, du kan fastställa baserat på testning, att ditt program bör ha en konsekvent procent slutförandenivå 90% eller 85%. Om din mätvärdesdata visar att programmet avviker från det antalet och sedan kan du undersöka vad orsakar ökningen.

När vi har etablerat att procent lyckade hastighet mått är mindre än 100%, i vårt Exempelscenario, kommer vi granska loggarna för att hitta de fel som stämmer med mätvärdena och använda dem för att ta reda på vad som orsakar lägre procent slutförandenivå. Vi ska titta särskilt på fel i 400 intervallet. Sedan ska vi närmare undersöka 404 (inget hittas) fel.

### <a name="some-causes-of-400-range-errors"></a>Vissa orsakerna till 400-range-fel
Exemplen nedan visas ett urval av fel 400-intervall för begäran mot Azure Blob Storage och deras möjliga orsaker. Något av dessa fel, samt fel i intervallet 300 och 500 intervallet kan bidra till en låg procent lyckade resultat.

Observera att listorna nedan är långt från klar. Se [Status och felkoder](http://msdn.microsoft.com/library/azure/dd179382.aspx) på MSDN för information om allmänna Azure Storage-fel och om fel som är specifika för varje storage-tjänster.

**Status 404 (inget hittas)-kodexempel**

Inträffar när en Läsåtgärd mot en behållare eller blobb misslyckas eftersom blob eller behållaren inte finns.

* Inträffar om en behållare eller blobb har tagits bort av en annan klient innan denna begäran.
* Inträffar om du använder ett API-anrop som skapar behållare eller blobb när du har kontrollerat om den finns. CreateIfNotExists APIs ringa HEAD först för att kontrollera förekomsten av behållare eller blobb; Om det inte finns ett 404-fel returneras och sedan en andra PUT-anrop görs för att skriva behållare eller blob.

**Status-kodexempel 409 (konflikt)**

* Inträffar om du använder en skapa API för att skapa en ny behållare eller blobb, utan att först kontrollera befintliga och en behållare eller blob med det namnet finns redan.
* Inträffar om en behållare tas bort och du försöker skapa en ny behållare med samma namn innan borttagningen är klar.
* Inträffar om du anger ett lån på en behållare eller blobb och det finns redan ett lån finns.

**Statuskod 412 (villkor misslyckades) exempel**

* Inträffar när det villkor som anges av ett villkorat huvud inte har uppfyllts.
* Inträffar när lånet-ID som angetts inte matchar lease-ID på behållaren eller blob.

## <a name="generate-log-files-for-analysis"></a>Skapa loggfiler för analys
I den här kursen använder vi Message Analyzer för att arbeta med tre olika typer av loggfiler, men du kan välja att arbeta med något av dessa:

* Den **serverloggen**, som skapas när du aktiverar loggning för Azure Storage. Serverloggen innehåller information om varje åtgärd som kallas mot en av tjänsterna Azure Storage - blob, kön, tabell och fil. Serverloggen anger vilken åtgärd anropades och vilka statuskoden var returnerade, samt annan information om begäran och svar.
* Den **.NET-klientloggen**, som skapas när du aktiverar loggning på klientsidan från .NET-program. Klientloggen innehåller detaljerad information om hur klienten förbereder begäran och tar emot och bearbetar svaret.
* Den **HTTP nätverket spårningslogg**, som samlar in data på HTTP/HTTPS-begäran och svar data, inklusive för åtgärder mot Azure Storage. I den här kursen ska vi generera spårning i nätverket via Message Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurera loggning på serversidan och mått
Först måste du konfigurera Azure Storage-loggning och statistik, så att vi har data från klientprogram att analysera. Du kan konfigurera loggning och mått i en mängd olika sätt – den [Azure-portalen](https://portal.azure.com), med hjälp av PowerShell, eller programmässigt. Se [aktivera Storage-mätvärden och visa mått Data](http://msdn.microsoft.com/library/azure/dn782843.aspx) och [aktivera loggning för lagring och åtkomst till loggdata](http://msdn.microsoft.com/library/azure/dn782840.aspx) på MSDN mer information om hur du konfigurerar loggning och mått.

**Via Azure portal**

Konfigurera loggning och mått för lagringen konto med hjälp av den [Azure-portalen](https://portal.azure.com), följer du anvisningarna [övervaka ett lagringskonto i Azure portal](storage-monitor-storage-account.md).

> [!NOTE]
> Det går inte att ange minut mått med Azure-portalen. Vi rekommenderar dock att du har angett dem för den här kursen och för att undersöka prestandaproblem med ditt program. Du kan ange minut mått med hjälp av PowerShell som visas nedan eller genom programmering med storage-klientbiblioteket.
> 
> Observera att Azure-portalen inte kan visa minut statistik, och endast timvis mått.
> 
> 

**Via PowerShell**

Om du vill komma igång med PowerShell för Azure, se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

1. Använd den [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0) för att lägga till användarkontot Azure PowerShell-fönstret:
   
    ```powershell
    Add-AzureAccount
    ```

2. I den **logga in på Microsoft Azure** fönstret skriver du e-postadress och lösenord som är kopplad till ditt konto. Azure autentiserar och sparar autentiseringsuppgifterna och stänger sedan fönstret.
3. Ange standardkontot för lagring till storage-konto som du använder för kursen genom att köra dessa kommandon i PowerShell-fönstret:
   
    ```powershell
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Aktivera loggning för lagring för Blob-tjänsten:
   
    ```powershell
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```

5. Aktivera storage-mätvärden för Blob-tjänsten, se till att ange **- MetricsType** till `Minute`:
   
    ```powershell
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Konfigurera loggning för klientsidan av .NET
Aktivera .NET diagnostik i programmets konfigurationsfil (web.config eller app.config) om du vill konfigurera loggning på klientsidan för en .NET-program. Se [klientsidan loggning med Storage-klientbiblioteket för .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx) och [klientsidan loggning med Microsoft Azure Storage SDK för Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) på MSDN mer information.

Klientsidans loggen innehåller detaljerad information om hur klienten förbereder begäran och tar emot och bearbetar svaret.

Storage-klientbiblioteket lagrar klientsidan loggdata i den plats som anges i programmets konfigurationsfil (web.config eller app.config).

### <a name="collect-a-network-trace"></a>Samla in en spårning i nätverket
Du kan använda analysverktyg för att samla in en spårning i nätverket för HTTP/HTTPS när klientprogrammet körs. Message Analyzer använder [Fiddler](http://www.telerik.com/fiddler) på serverdelen. Innan du samlar in spårning i nätverket, rekommenderar vi att du konfigurerar Fiddler för att registrera okrypterad HTTPS-trafik:

1. Installera [Fiddler](http://www.telerik.com/download/fiddler).
2. Starta Fiddler.
3. Välj **verktyg | Alternativ för fiddler**.
4. I dialogrutan Alternativ kontrollerar du att **avbilda HTTPS ansluter** och **dekryptera HTTPS-trafik** båda är valda, enligt nedan.

![Konfigurera alternativ för Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Samla in och spara en spårning i nätverket först i Message Analyzer och skapar en analys-session för att analysera spårningen och loggar för självstudierna. Att samla in en spårning i nätverket i Message Analyzer:

1. Välj i Message Analyzer **filen | Snabb Trace | Okrypterade HTTPS**.
2. Spårningen startar omedelbart. Välj **stoppa** att stoppa spårningen så att vi kan konfigurera att spåra lagringsrelaterad trafik.
3. Välj **redigera** redigera spårningssessionen.
4. Välj den **konfigurera** länken till höger om den **Microsoft-Pef-WebProxy** ETW-provider.
5. I den **avancerade inställningar** dialogrutan klickar du på den **Provider** fliken.
6. I den **filtret Hostname** anger dina slutpunkter för lagring, avgränsade med blanksteg. Exempelvis kan du ange dina slutpunkter enligt följande; ändra `storagesample` till namnet på ditt lagringskonto:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Avslutar dialogrutan och klicka på **starta om** att börja samla in spårningen med hostname-filter på plats, så att endast Azure Storage-nätverkstrafik ingår i spårningen.

> [!NOTE]
> När du är klar att samla in en spårning i nätverket, rekommenderar vi starkt att du återställa de inställningar som du har ändrat i Fiddler att dekryptera HTTPS-trafik. I dialogrutan Alternativ för Fiddler avmarkera den **avbilda HTTPS ansluter** och **dekryptera HTTPS-trafik** kryssrutorna.
> 
> 

Se [använder nätverksfunktioner spårning](http://technet.microsoft.com/library/jj674819.aspx) på Technet för mer information.

## <a name="review-metrics-data-in-the-azure-portal"></a>Granska mått data i Azure-portalen
När programmet har körts för en viss tidsperiod, kan du granska mått diagrammen som visas i den [Azure-portalen](https://portal.azure.com) vill se hur tjänsten fungerar.

Först gå till ditt lagringskonto i Azure-portalen. Som standard en övervakning diagram med de **lyckade procentandel** mått visas i bladet konto. Om du tidigare har ändrat diagrammet om du vill visa olika mått, lägger du till den **lyckade procentandel** mått.

Nu visas **lyckade procentandel** i Övervakningsdiagrammet, tillsammans med andra mått du kanske har lagt till. I det scenario som vi ska undersöka bredvid genom att analysera loggar i Message Analyzer är procent lyckade resultat uppträder under 100%.

Mer information om att lägga till och anpassar mått diagram finns [anpassa mått diagram](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Det kan ta lite tid för dina mått data ska visas i Azure-portalen när du aktiverar lagring mått. Det beror på att varje timme mätvärden för föregående timma inte visas i Azure portal tills den aktuella timman har gått ut. Dessutom visas minut mått för närvarande inte i Azure-portalen. Så beroende på när du aktiverar mått, det kan ta upp till två timmar att se data för mått.
> 
> 

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Använd AzCopy för att kopiera server-loggar till en lokal katalog
Azure Storage skriver server loggdata till blobbar, medan mätvärdena skrivs till tabeller. Loggen blobbar är tillgängliga i den välkända `$logs` behållare för ditt lagringskonto. Loggen blobbar namnet hierarkiskt som år, månad, dag och timme, så att du lätt kan hitta tidsperiod som du vill undersöka. Till exempel i den `storagesample` kontot behållaren för logg-BLOB för 01/02/2015 från 8 9 am, är `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Enskilda blobbar i den här behållaren har sekventiella namn, från och med `000000.log`.

Du kan använda kommandoradsverktyget AzCopy för att ladda ned dessa loggfiler för servern till en plats på den lokala datorn. Du kan till exempel använda följande kommando för att hämta loggfilerna för blob-åtgärder som utfördes på 2 januari 2015 till mappen `C:\Temp\Logs\Server`; Ersätt `<storageaccountname>` med namnet på ditt lagringskonto och `<storageaccountkey>` med din åtkomstnyckel:

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy är tillgänglig för hämtning på den [Azure hämtar](https://azure.microsoft.com/downloads/) sidan. Mer information om hur du använder AzCopy finns [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md).

Mer information om hur du hämtar serversidan loggar finns [hämta lagring loggning loggdata](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Använda Microsoft Message Analyzer för att analysera loggdata
Microsoft Message Analyzer är ett verktyg för att samla in, visa och analysera protokollet messaging trafik, händelser och andra system- eller meddelanden i scenarier med felsökning och diagnostik. Message Analyzer också kan du läsa in, sammanställa och analysera data från loggen och sparas spårningsfiler. Mer information om Message Analyzer finns [operativsystem handboken för Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx).

Message Analyzer innehåller tillgångar för Azure Storage som hjälper dig att analysera servern, klienten och loggar för nätverket. I det här avsnittet diskuterar vi hur du använder dessa verktyg för att åtgärda problemet för låg procent lyckas i loggarna för lagring.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Hämta och installera Message Analyzer och Azure Storage-tillgångar
1. Hämta [Message Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) från Microsoft Download Center och kör installationsprogrammet.
2. Starta Message Analyzer.
3. Från den **verktyg** väljer du **Tillgångsansvarig**. I den **Tillgångsansvarig** markerar **hämtar**, sedan filtrera **Azure Storage**. Azure Storage tillgångar, visas som visas i bilden nedan.
4. Klicka på **synkronisera alla visas objekt** att installera Azure Storage tillgångar. De tillgängliga resurserna är:
   * **Azure Storage-färgregler:** färgregler för Azure Storage kan du definiera särskilda filter som använder färg, text och format för att fokusera på meddelanden som innehåller specifik information i en spårning.
   * **Azure Storage-diagram:** diagram för Azure Storage är fördefinierade diagram som diagramdata server-loggen. Observera att om du vill använda Azure Storage diagram just nu, kan endast läsa serverloggen i rutnätet för analys.
   * **Azure Storage-Parser:** Azure Storage-Parser tolka Azure Storage-klienten, server och HTTP-loggarna för att visa dem i rutnätet analys.
   * **Azure Storage-filter:** Azure Storage filter är fördefinierade kriterier som du kan använda för att fråga data i rutnätet analys.
   * **Azure Storage layouter:** layouter Azure Storage är fördefinierad kolumn och grupperingar i rutnätet analys.
5. Starta om Message Analyzer när du har installerat tillgångar.

![Message Analyzer Asset Manager](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Installera alla Azure Storage-tillgångar som visas för den här kursen.
> 
> 

### <a name="import-your-log-files-into-message-analyzer"></a>Importera loggfilerna till Message Analyzer
Du kan importera alla dina sparade loggfiler (servern, klienten och nätverk) till en enda session i Microsoft Message Analyzer för analys.

1. På den **filen** Klicka på menyn i Microsoft Message Analyzer **ny Session**, och klicka sedan på **tom Session**. I den **ny Session** dialogrutan, ange ett namn för din session för analys. I den **sessionsinformation** panelen, klickar du på den **filer** knappen.
2. Klicka på för att läsa in nätverket spåra data som genereras av Message Analyzer **Lägg till filer**, bläddra till platsen där du sparade filen .matp från webben spårningssessionen, markera filen .matp och klicka på **öppna**.
3. Om du vill läsa in loggdata för serversidan, klickar du på **Lägg till filer**, bläddra till den plats där du sparade loggarna serversidan, väljer du loggfilerna för det tidsintervall som du vill analysera och klicka på **öppna**. I den **sessionsinformation** panelen genom att ange den **Text konfigurationen av loggen** listrutan för varje serversidan loggfilen till **AzureStorageLog** så att Microsoft Message Analyzer kan parsa loggfilen på rätt sätt.
4. Klicka på för att läsa in loggdata för klientsidan **Lägg till filer**, bläddra till platsen där du sparade loggarna på klientsidan, välja loggfiler som du vill analysera och klicka på **öppna**. I den **sessionsinformation** panelen genom att ange den **Text konfigurationen av loggen** listrutan för varje klientsidan loggfilen till **AzureStorageClientDotNetV4** så att Microsoft Message Analyzer kan parsa loggfilen på rätt sätt.
5. Klicka på **starta** i den **ny Session** för att läsa in och tolka loggdata. Loggdata som visas i rutnätet Message Analyzer analys.

I bilden nedan visas en exempel-session som har konfigurerats med servern, klienten och spårningsloggfilerna i nätverket.

![Konfigurera Message Analyzer Session](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Observera att Message Analyzer läser in loggfiler i minnet. Om du har en stor mängd loggade data, kommer du vill filtrera för att få bästa möjliga prestanda från Message Analyzer.

Först fastställa den tidsperiod som du vill granska och att denna tidsram så liten som möjligt. I många fall vill du granska en tidsperiod minuter eller timmar högst. Importera den minsta mängden loggar som uppfyller dina behov.

Om du fortfarande har en stor mängd loggdata vill ange en session och filtrerar logga data innan du läsa in den. I den **Sessionsfiltret** markerar den **biblioteket** knappen om du vill välja ett fördefinierat filter, till exempel välja **Global tid Filter I** från Azure Storage filter för att filtrera på ett tidsintervall. Du kan sedan redigera filtervillkoren om du vill ange de inledande och avslutande tidsstämpel för intervallet som du vill se. Du kan också filtrera på en viss statuskod; Du kan exempelvis välja att läsa in endast loggposter där statuskoden är 404.

Mer information om att importera loggdata till Microsoft Message Analyzer finns [hämtar meddelandedata](http://technet.microsoft.com/library/dn772437.aspx) på TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Använd ID för klientbegäran för att korrelera data för loggfil
Azure Storage-klientbiblioteket genererar automatiskt ett unikt ID för klientbegäran för varje begäran. Det här värdet skrivs till klientloggen och serverloggen spårning i nätverket, så du kan använda för att korrelera data över alla tre loggar inom Message Analyzer. Se [ID för klientbegäran](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) för ytterligare information om klienten begär ID.

I avsnitten nedan beskrivs hur du använder layouten förkonfigurerade och anpassade vyer för korrelation och gruppera data baserat på klient-begäran-ID.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Välj en layout för vyn ska visas i rutnätet analys
Storage-resurser för Message Analyzer inkluderar Azure Storage layouter, som är förkonfigurerad vyer som du kan använda för att visa dina data med användbar grupperingar och kolumner för olika scenarier. Du kan också skapa anpassade layouter och spara dem för återanvändning.

Bilden nedan visar den **visa Layout** menyn genom att välja **visa Layout** från verktygsfältet menyfliksområdet. Visa layouter för Azure Storage är grupperade under den **Azure Storage** nod i menyn. Du kan söka efter `Azure Storage` Visa endast layouter i sökrutan för att filtrera på Azure Storage. Du kan också välja star bredvid layouten vyn att göra det en favorit och visa det överst på menyn.

![Visa menyn Layout](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Börja med att välja **grupperade efter ClientRequestID och modulen**. Den här vyn layout grupper logga data från alla tre loggar först efter ID för klientbegäran, sedan efter loggfil för källdatorn (eller **modulen** i Message Analyzer). Med den här vyn kan du detaljnivån i en viss klient-ID för förfrågan och se data från alla tre loggfiler för klientbegäran-ID.

Bilden nedan visar den här layoutvyn tillämpas på loggen exempeldata, med en delmängd med kolumner som visas. Du kan se att analys rutnätet för en viss klient begärande-ID visar data från den klientloggen och serverloggen spårning i nätverket.

![Azure Storage visa Layout](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Olika loggfilerna har olika kolumner, så när data från flera loggfiler visas i rutnätet analys, vissa kolumner inte får innehålla några data för en viss rad. Till exempel i bilden ovan klienten loggen rader visas inte alla data för den **tidsstämpel**, **TimeElapsed**, **källa**, och **mål** kolumner, eftersom dessa kolumner finns inte i klientloggen för, men finns i spårning i nätverket. På liknande sätt den **tidsstämpel** visar kolumnen tidsstämpel data från serverloggen, men inga data visas för den **TimeElapsed**, **källa**, och **mål** kolumner som inte ingår i serverloggen.
> 
> 

Förutom att använda layouter i Azure Storage kan du också definiera och spara egna layouter. Du kan välja andra önskade fält för gruppering av data och spara grupperingen som en del av din anpassade layout.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Tillämpa färgregler på rutnätet Analysis
Storage-tillgångar också innefatta färgregler som ger en visuell innebär att identifiera olika typer av fel i rutnätet analys. Fördefinierade färgregler gäller för HTTP-fel, så att de visas endast för serverspårning loggen och nätverk.

Markera för att tillämpa färgregler **färgregler** från verktygsfältet menyfliksområdet. Regler för Azure Storage-färg i menyn visas. Kursen, Välj **klientfel (StatusCode mellan 400 och 499)** som visas i bilden nedan.

![Azure Storage visa Layout](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Förutom att använda färgregler för Azure Storage kan du också definiera och spara dina egna färgregler.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Gruppen och filtrera loggdata för att hitta 400-range-fel
Nu ska vi gruppen och filtrera loggdata för att hitta alla fel i 400 intervallet.

1. Leta upp den **StatusCode** kolumn i rutnätet analys högerklickar du på kolumnen rubrik och välj **gruppen**.
2. Därefter gruppen på den **ClientRequestId** kolumn. Ser du att data i rutnätet analys nu är ordnad efter statuskod och genom att klient-begäran-ID.
3. Visa fönstret Visa Filter-verktyget om den inte redan visas. Välj på menyfliken verktygsfältet **verktyget Windows**, sedan **visningsfilter**.
4. Om du vill filtrera loggdata för att visa endast 400 intervallet fel, lägger du till följande filterkriterier för att den **visningsfilter** , och klicka på **Verkställ**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

Bilden nedan visar resultatet av den här grupperingen och filter. Expandera den **ClientRequestID** fältet under gruppering för statuskod 409, till exempel visar en åtgärd som resulterade i att statuskod.

![Azure Storage visa Layout](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Efter att det här filtret, ser du att rader från klientloggen undantas, som klienten loggen inte innehåller en **StatusCode** kolumn. Börja med att vi ska granska server och nätverk spårningsloggar att hitta 404-fel och vi återkommer till klientloggen att undersöka Klientåtgärder som ledde till dem.

> [!NOTE]
> Du kan filtrera efter den **StatusCode** kolumn och fortfarande visa data från alla tre loggar, inklusive klientloggen om du lägger till ett uttryck filter som innehåller poster där statuskoden är null. Använd för att skapa den här filteruttrycket:
> 
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
> 
> Det här filtret returnerar alla rader från klienten loggen och endast rader från serverloggen och HTTP-loggen där statuskoden är större än 400. Om du använder för att visa layouten grupperade efter ID för klientbegäran och modulen, du kan söka eller bläddra igenom loggposter för att hitta sådana där alla tre loggar visas.   
> 
> 

### <a name="filter-log-data-to-find-404-errors"></a>Filtrera loggdata för att hitta 404-fel
Storage-tillgångar inkluderar fördefinierade filter som du kan använda för att begränsa loggdata för att söka efter fel eller trender som du letar efter. Nu ska vi ska använda två fördefinierade filter: en som filtrerar server och nätverk spårningsloggar för 404-fel och en som filtrerar data på ett angivet tidsintervall.

1. Visa fönstret Visa Filter-verktyget om den inte redan visas. Välj på menyfliken verktygsfältet **verktyget Windows**, sedan **visningsfilter**.
2. I fönstret Visa Filter Välj **biblioteket**, och Sök på `Azure Storage` hitta Azure Storage-filter. Välj filtret för **404 (inget hittas) meddelanden i alla loggar**.
3. Visa den **biblioteket** menyn igen, och leta upp och markera den **globala tidsfiltret**.
4. Redigera tidsstämplar som visas i filtret på intervallet som du vill visa. Detta hjälper för att begränsa mängd data för att analysera.
5. Filtret bör likna exemplet nedan. Klicka på **tillämpa** att tillämpa filtret analys rutnätet.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Azure Storage visa Layout](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analysera dina loggdata
Nu när du har grupperats och filtreras data kan granska du information om enskilda förfrågningar som genererade fel 404. I den aktuella vyn layouten grupperas data efter ID för klientbegäran, sedan efter källan. Eftersom vi filtrerar på begäranden där fältet StatusCode innehåller 404 kan se vi servern och nätverket spårningsdata, inte klienten loggdata.

Bilden nedan visar en specifik begäran där en hämta Blob-åtgärd gav en 404 eftersom blobben inte finns. Observera att vissa kolumner har tagits bort från vyn standard för att visa relevanta data.

![Filtrerade Server och nätverk spårningsloggar](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Vi kommer därefter korrelera detta klient-ID för begäran med loggdata klienten se vad klienten tog när felet inträffade. Du kan visa en ny analys rutnätsvy för den här sessionen att visa loggdata klienten som öppnas i andra fliken:

1. Först kopiera värdet för den **ClientRequestId** till Urklipp. Du kan göra detta genom att välja antingen rad, lokalisera den **ClientRequestId** fältet, högerklicka på datavärdet och välja **kopiera 'ClientRequestId'**.
2. Markera på menyfliken verktygsfältet **nya visningsprogrammet för**och välj **analys rutnätet** att öppna en ny flik. Den nya fliken visar alla data i loggfilerna, utan gruppering, filtrering och färgregler.
3. På menyfliken verktygsfältet väljer **visa Layout**och välj **alla .NET klienten kolumner** under den **Azure Storage** avsnitt. Layout för den här vyn visar data från klienten loggen samt spårningsloggar server och nätverk. Som standard den sorteras på den **MessageNumber** kolumn.
4. Nu söka klientloggen för klient-begäran-ID. På menyfliken verktygsfältet väljer **hitta meddelanden**, ange ett filter på klient-ID för begäran i den **hitta** fältet. Använd följande syntax för filtret, ange din egen klient-ID för begäran:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Message Analyzer hittar och väljer den första loggposten där matchar sökkriterierna klienten begärande-ID. Klientloggen, finns det flera poster för varje ID för klientbegäran, så kanske du vill gruppera dem på den **ClientRequestId** fält som du vill göra det lättare att se dem samtidigt. I bilden nedan visas alla meddelanden i klientloggen för för den angivna klienten begärande-ID.

![Klienten loggen visar 404-fel](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Du kan analysera begärandedata för att avgöra vad kan ha orsakat felet med hjälp av data som visas i vyn layouter i dessa två flikar. Du kan också se begäranden som föregås den för att se om en tidigare händelse har lett till 404-fel. Du kan till exempel granska klienten loggposter före detta klient-ID för begäran om att avgöra om blob kan ha tagits bort, eller om felet beror på att klientprogram anropar en CreateIfNotExists API på en behållare eller blobb. I klientloggen hittar du i blob-adress i den **beskrivning** fältet; i servern och nätverket spårningsloggar, visas informationen i den **sammanfattning** fältet.

När du vet adressen till blob som gav 404-fel kan undersöka du vidare. Du kan kontrollera om klienten tidigare bort entiteten om du söker loggposter för andra meddelanden som är associerade med åtgärder på samma blob.

## <a name="analyze-other-types-of-storage-errors"></a>Analysera andra typer av lagring fel
Nu när du är bekant med Message Analyzer för att analysera dina loggdata kan du analysera andra typer av fel i vyn layouter, färgregler och söka/filtrering. Tabellerna nedan listas några problem som kan uppstå och filtervillkoren som du kan använda för att hitta dem. Mer information om konstruera filter och analysverktyg som filtrering språk finns [filtrering meddelandedata](http://technet.microsoft.com/library/jj819365.aspx).

| Att undersöka... | Använd filteruttrycket... | Uttryck som gäller för Log (klient, Server, nätverk, alla) |
| --- | --- | --- |
| Oväntade fördröjningar i leverans av meddelanden i en kö |AzureStorageClientDotNetV4.Description innehåller ”försöker på nytt misslyckades igen”. |Klient |
| HTTP-ökning av PercentThrottlingError |HTTP. Response.StatusCode == 500 &#124; &#124; HTTP. Response.StatusCode == 503 |Nätverk |
| Öka i PercentTimeoutError |HTTP. Response.StatusCode == 500 |Nätverk |
| Öka i PercentTimeoutError (alla) |* StatusCode == 500 |Alla |
| Öka i PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Klient |
| HTTP 403 (nekas) meddelanden |HTTP. Response.StatusCode == 403 |Nätverk |
| HTTP 404 (inget hittas) meddelanden |HTTP. Response.StatusCode == 404 |Nätverk |
| 404 (alla) |* StatusCode == 404 |Alla |
| Delade signatur åtkomst (SAS) auktorisering problemet |AzureStorageLog.RequestStatus == ”SASAuthorizationError” |Nätverk |
| HTTP 409 (konflikt) meddelanden |HTTP. Response.StatusCode == 409 |Nätverk |
| 409 (alla) |* StatusCode == 409 |Alla |
| Låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus för ClientOtherErrors |AzureStorageLog.RequestStatus == ”ClientOtherError” |Server  |
| Nagle varning |((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) och (AzureStorageLog.RequestPacketSize < 1 460) och (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200) |Server  |
| Tidsintervall i Server- och loggar |#Timestamp > = 2014-10-20T16:36:38 och #Timestamp < = 2014-10-20T16:36:39 |Server, Network |
| Tidsintervall i Server-loggar |AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 och AzureStorageLog.Timestamp < = 2014-10-20T16:36:39 |Server  |

## <a name="next-steps"></a>Nästa steg
Mer information om felsökning slutpunkt till slutpunkt-scenarier i Azure Storage finns i följande resurser:

* [Övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Lagringsanalys](http://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Övervaka ett lagringskonto i Azure-portalen](storage-monitor-storage-account.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Microsoft Message Analyzer fungerar Guide](http://technet.microsoft.com/library/jj649776.aspx)
