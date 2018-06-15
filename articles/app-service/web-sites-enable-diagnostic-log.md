---
title: Aktivera diagnostikloggning för web apps i Azure App Service
description: Lär dig hur du aktiverar du diagnostikloggning och lägga till instrumentation i ditt program, samt hur du kommer åt information som loggas av Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.openlocfilehash: 15c580a026495d11ffdeb161d4bf0793850040f5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32158774"
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Aktivera diagnostikloggning för web apps i Azure App Service
## <a name="overview"></a>Översikt
Azure tillhandahåller inbyggda diagnostik för att underlätta felsökning en [Apptjänst webbapp](http://go.microsoft.com/fwlink/?LinkId=529714). I den här artikeln lär du dig aktivera diagnostikloggning och lägga till instrumentation i ditt program, samt hur du kommer åt information som loggas av Azure.

Den här artikeln används den [Azure-portalen](https://portal.azure.com), Azure PowerShell och Azure-kommandoradsgränssnittet (Azure CLI) för att fungera med diagnostikloggar. Information om hur du arbetar med diagnostikloggar med Visual Studio finns i [felsöka Azure i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Web serverdiagnostik- och programdiagnostik
App Service web apps ange diagnostikfunktion för att logga information från både webbservern och webbprogrammet. Dessa logiskt är indelade i **web serverdiagnostik** och **programdiagnostik**.

### <a name="web-server-diagnostics"></a>Web serverdiagnostik
Du kan aktivera eller inaktivera följande typer av loggar:

* **Detaljerad felloggning** -information om felet för HTTP-statuskoder som indikerar att en (statuskod 400 eller högre). Den kan innehålla information som kan hjälpa dig att avgöra varför servern returnerade felkoden.
* **Kunde inte begäran spårning** -detaljerad information om misslyckade förfrågningar, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och tidsåtgång i varje komponent. Det är användbart om du försöker öka prestanda för webbplatsen eller isolera vad som orsakar ett specifikt HTTP-fel som ska returneras.
* **Web Server-loggning** -Information om HTTP-transaktioner med hjälp av den [W3C utökat loggfilsformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Det är användbart när du fastställer övergripande plats mätvärden, till exempel antalet förfrågningar som hanteras eller hur många förfrågningar som kommer från en specifik IP-adress.

### <a name="application-diagnostics"></a>Programdiagnostik
Programdiagnostik kan du samla in information som produceras av ett webbprogram. ASP.NET-program kan använda den [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) klassen för att logga information till diagnostik programloggen. Exempel:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Vid körning kan hämta du dessa loggar för felsökning. Mer information finns i [felsöka Azure web apps i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

App Service web apps även logga information om distribution när du publicerar innehåll till en webbapp. Det sker automatiskt och det finns inga konfigurationsinställningar för loggning för distribution. Loggning för distribution kan du avgöra varför en distribution misslyckades. Till exempel om du använder ett anpassat distributionsskriptet, kan du använda deployment loggning för att avgöra varför skriptet inte.

## <a name="enablediag"></a>Så här aktiverar du diagnostik
Aktivera diagnostik i den [Azure-portalen](https://portal.azure.com), gå till sidan för webbappen och klickar på **Inställningar > diagnostik loggar**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![En del loggar](./media/web-sites-enable-diagnostic-log/logspart.png)

När du aktiverar **programdiagnostik**, du också välja den **nivå**. Den här inställningen kan du filtrera den information som har hämtats till **informationsmeddelande**, **varning**, eller **fel** information. Ange värdet till **utförlig** loggar all information som genereras av programmet.

> [!NOTE]
> Till skillnad från ändrar web.config-filen, återanvändning aktiverar programdiagnostik eller ändrar diagnostiska loggningsnivåer tillämpningsdomän som programmet körs inom.
>
>

För **programloggning**, du kan aktivera alternativet filen system tillfälligt för felsökning. Det här alternativet inaktiverar automatiskt i 12 timmar. Du kan också aktivera blob storage alternativet att skriva loggar till en blob-behållare.

För **Web server-loggning**, kan du välja **lagring** eller **filsystem**. Att välja **lagring** kan du välja ett lagringskonto och en blob-behållare som loggarna skrivs till. 

Om du sparar loggar i filsystemet, kan filerna som nås av FTP, eller hämtas som en Zip-arkiv med hjälp av Azure PowerShell eller Azure-kommandoradsgränssnittet (Azure CLI).

Som standard loggar tas inte bort automatiskt (med undantag av **programloggning (filsystem)**). Ta bort loggar automatiskt, ange den **kvarhållningsperiod (dagar)** fältet.

> [!NOTE]
> Om du [återskapa åtkomstnycklar för ditt lagringskonto](../storage/common/storage-create-storage-account.md), måste du återställa respektive loggningsinställningarna för att använda de uppdaterade nycklarna. Gör så här:
>
> 1. I den **konfigurera** fliken genom att ange respektive loggningsfunktionen **av**. Spara dina inställningar.
> 2. Aktivera loggning för kontot lagringsblob eller tabell igen. Spara dina inställningar.
>
>

En kombination av filsystemet, tabellagring eller blob-lagring kan aktiveras på samma gång och har enskild logg nivån konfigurationer. Exempelvis kanske du vill logga fel och varningar till blob storage som en långsiktig loggning lösning för att aktivera filen systemloggning med utförlig nivå.

När alla tre lagringsplatser ange samma grundläggande information för loggade händelser **tabell lagring** och **blob storage** logga ytterligare information, till exempel instans-ID, tråd-ID och en mer detaljerad tidsstämpel (skalstreck format) än loggning till **filsystem**.

> [!NOTE]
> Information som lagras i **tabell lagring** eller **blob storage** kan bara användas med en storage-klient eller ett program som kan arbeta direkt med dessa lagringssystem. Till exempel Visual Studio 2013 innehåller en lagringsutforskare som kan användas för att utforska tabell eller blob storage och HDInsight kan komma åt data som lagras i blob storage. Du kan också skriva ett program som har åtkomst till Azure Storage med hjälp av en av de [Azure SDK](/downloads/#).
>
> [!NOTE]
> Diagnostiken kan även aktiveras från Azure PowerShell med hjälp av den **Set AzureWebsite** cmdlet. Om du inte har installerat Azure PowerShell eller inte har konfigurerat den att använda Azure-prenumeration, se [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0).
>
>

## <a name="download"></a> Så här: hämta loggar
Diagnostisk information som lagras i filsystemet app kan nås med hjälp av FTP. Det kan också hämtas som en Zip-arkiv med hjälp av Azure PowerShell eller Azure-kommandoradsgränssnittet.

Katalogstrukturen som lagrats i loggarna är som följer:

* **Programloggarna** -/LogFiles/program /. Den här mappen innehåller en eller flera textfiler som innehåller information som genereras av programloggning.
* **Det gick inte begäranden** -/ loggfiler/W3SVC ### /. Den här mappen innehåller en XSL-fil och en eller flera XML-filer. Se till att du hämtar den XSL-fil i samma katalog som XML-filerna eftersom XSL-filen innehåller funktioner för att formatera och filtrera innehållet i XML-filen när den visas i Internet Explorer.
* **Detaljerade felloggar** -/LogFiles/DetailedErrors /. Den här mappen innehåller en eller flera .htm-filer som innehåller omfattande information för HTTP-fel som har inträffat.
* **Web Server-loggar** -/LogFiles/http/RawLogs. Den här mappen innehåller en eller flera textfiler formaterats med den [W3C utökat loggfilsformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Distributionsloggar** -loggfilerna/Git. Den här mappen innehåller loggar som genereras av de interna distributionsprocesser som används av Azure-webbappar samt loggar för Git-distributioner. Du kan också hitta distributionsloggar under D:\home\site\deployments.

### <a name="ftp"></a>FTP

Om du vill öppna en FTP-anslutning till FTP-server för din app Se [distribuera din app till Azure App Service med FTP/S](app-service-deploy-ftp.md).

När du är ansluten till ditt webbprogram FTP/S-servern, öppna den **loggfiler** mappen, där filerna lagras.

### <a name="download-with-azure-powershell"></a>Hämta med Azure PowerShell
Starta en ny instans av Azure PowerShell för att hämta filerna, och använder du följande kommando:

    Save-AzureWebSiteLog -Name webappname

Detta kommando sparar loggar för webbprogram som anges av den **-namnet** parameter till en fil med namnet **logs.zip** i den aktuella katalogen.

> [!NOTE]
> Om du inte har installerat Azure PowerShell eller inte har konfigurerat den att använda Azure-prenumeration, se [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0).
>
>

### <a name="download-with-azure-command-line-interface"></a>Hämta med Azure-kommandoradsgränssnittet
För att hämta filerna med hjälp av Azure-kommandoradsgränssnittet, öppna en ny kommandotolk, PowerShell, Bash eller terminalsession och ange följande kommando:

    az webapp log download --resource-group resourcegroupname --name webappname

Detta kommando sparar loggar för webbprogrammet med namnet 'webappname' till en fil med namnet **diagnostics.zip** i den aktuella katalogen.

> [!NOTE]
> Om du inte har installerat Azure-kommandoradsgränssnittet (Azure CLI) eller inte har konfigurerat den att använda Azure-prenumeration, se [hur du använder Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Så här: visa loggar i Application Insights
Programinsikter för Visual Studio innehåller verktyg för att filtrera och söka i loggar och för korrelation mellan loggarna med begäranden och andra händelser.

1. Lägg till Application Insights SDK i ditt projekt i Visual Studio.
   * Högerklicka på projektet i Solution Explorer och välj Lägg till Application Insights. Gränssnittet vägleder dig genom stegen som omfattar att skapa en Application Insights-resurs. [Läs mer](../application-insights/app-insights-asp-net.md)
2. Lägga till spårningsavlyssningen paket i projektet.
   * Högerklicka på projektet och välj Hantera NuGet-paket. Välj `Microsoft.ApplicationInsights.TraceListener` [Läs mer](../application-insights/app-insights-asp-net-trace-logs.md)
3. Överför ditt projekt och kör det att generera loggdata.
4. I den [Azure-portalen](https://portal.azure.com/), bläddra till din nya Application Insights-resurs och öppna **Sök**. Du bör se din loggdata tillsammans med begäran, användning och andra telemetri. Vissa telemetri kan ta några minuter innan den tas emot: Klicka på Uppdatera. [Läs mer](../application-insights/app-insights-diagnostic-search.md)

[Mer information om prestanda spårning med Application Insights](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a> Så här: strömma loggar
När du utvecklar ett program, är det ofta praktiskt att visa loggningsinformation i nära realtid. Du kan strömma loggningsinformation till din utvecklingsmiljö med hjälp av Azure PowerShell eller Azure-kommandoradsgränssnittet.

> [!NOTE]
> Vissa typer av loggning buffert skriva till loggfilen, vilket kan leda till oordnade händelser i dataströmmen. Exempelvis kan en loggpost för program som uppstår när en användare besöker en sida visas i dataströmmen innan motsvarande HTTP-loggpost för begäran.
>
> [!NOTE]
> Loggen strömning också strömmar information skrivs till en textfil som lagras i den **D:\\hem\\loggfiler\\**  mapp.
>
>

### <a name="streaming-with-azure-powershell"></a>Strömning med Azure PowerShell
Att strömma loggningsinformation och starta om en ny instans av Azure PowerShell använder du följande kommando:

    Get-AzureWebSiteLog -Name webappname -Tail

Det här kommandot ansluter till det webbprogram som anges av den **-namnet** parameter och börja strömning information till PowerShell-fönstret när händelser inträffar kontinuerligt i webbprogrammet. All information som skrivs till filer som slutar på .txt, .log eller .htm som lagras i katalogen /LogFiles (d:/home/loggfilerna) strömmas till den lokala konsolen.

För att filtrera specifika händelser, till exempel fel, använder den **-meddelandet** parameter. Exempel:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

För att filtrera viss loggning typer, till exempel HTTP, använder den **-sökvägen** parameter. Exempel:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Använd parametern - ListPath om du vill se en lista över tillgängliga sökvägar.

> [!NOTE]
> Om du inte har installerat Azure PowerShell eller inte har konfigurerat den att använda Azure-prenumeration, se [hur du använder Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="streaming-with-azure-command-line-interface"></a>Strömning med Azure-kommandoradsgränssnittet
Strömma loggningsinformation, öppna en ny kommandotolk, PowerShell, Bash eller terminalsession och ange följande kommando:

    az webapp log tail --name webappname --resource-group myResourceGroup

Det här kommandot ansluter till webbprogrammet med namnet 'webappname' och påbörja strömning information till fönstret när händelser inträffar kontinuerligt i webbprogrammet. All information som skrivs till filer som slutar på .txt, .log eller .htm som lagras i katalogen /LogFiles (d:/home/loggfilerna) strömmas till den lokala konsolen.

För att filtrera specifika händelser, till exempel fel, använder den **--Filter** parameter. Exempel:

    az webapp log tail --name webappname --resource-group myResourceGroup --filter Error

För att filtrera viss loggning typer, till exempel HTTP, använder den **--sökväg** parameter. Exempel:

    az webapp log tail --name webappname --resource-group myResourceGroup --path http

> [!NOTE]
> Om du inte har installerat Azure-kommandoradsgränssnittet eller inte har konfigurerat den att använda Azure-prenumeration, se [så att använda Azure-kommandoradsgränssnittet](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Så här: Förstå diagnostik loggar
### <a name="application-diagnostics-logs"></a>Programloggarna för diagnostik
Programdiagnostik lagrar information i ett specifikt format för .NET-program, beroende på om du lagrar loggar till filsystemet, tabellagring eller blob-lagring. En grundläggande uppsättning data som lagras är samma i alla tre lagringstyper - datum och tid som händelsen inträffade, process-ID som producerade händelsen, vilken typ av händelse (information, varning, fel) och händelsemeddelandet.

**Filsystem**

Varje rad inloggade filsystemet eller tas emot med streaming är i följande format:

    {Date}  PID[{process ID}] {event type/level} {message}

Till exempel skulle en felhändelse liknar följande exempel:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Loggning till filsystemet ger den mest grundläggande informationen av tre tillgängliga metoder ger endast tid, process-ID, Händelsenivå och meddelandet.

**Table Storage**

När de loggar till tabellagring används ytterligare egenskaper som för att underlätta söka data som lagras i tabellen samt mer detaljerad information om händelsen. Följande egenskaper (kolumner) används för varje entitet (rad) som lagras i tabellen.

| Egenskapsnamn | Value-format |
| --- | --- |
| PartitionKey |Datum/tid för händelse i yyyyMMddHH format |
| RowKey |Ett GUID-värde som unikt identifierar den här entiteten |
| Tidsstämpel |Datum och tid då händelsen inträffade |
| EventTickCount |Datum och tid då händelsen inträffade i Tick-format (större precision) |
| ApplicationName |Webbprogramnamnet |
| Nivå |Händelsenivå (till exempel fel, varning, information) |
| EventId |Händelse-ID för den här händelsen<p><p>Standardvärdet är 0 om inget anges |
| Instans-ID |Instans av webbappen som den även inträffade |
| Process-ID |Process-ID |
| tid |Tråd-ID för tråden som skapades av händelsen |
| Meddelande |Detaljerat meddelande |

**Blob Storage**

När loggning för att blob storage, lagras data i fil med kommaavgränsade värden (CSV)-format. Liknar tabellagring, ytterligare fält som loggas med mer detaljerad information om händelsen. Följande egenskaper används för varje rad i CSV-filen:

| Egenskapsnamn | Value-format |
| --- | --- |
| Date |Datum och tid då händelsen inträffade |
| Nivå |Händelsenivå (till exempel fel, varning, information) |
| ApplicationName |Webbprogramnamnet |
| Instans-ID |Instans av webbappen som händelsen inträffade |
| EventTickCount |Datum och tid då händelsen inträffade i Tick-format (större precision) |
| EventId |Händelse-ID för den här händelsen<p><p>Standardvärdet är 0 om inget anges |
| Process-ID |Process-ID |
| tid |Tråd-ID för tråden som skapades av händelsen |
| Meddelande |Detaljerat meddelande |

Data som lagras i en blob som ser ut som följande exempel:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Den första raden i loggen innehåller kolumnrubrikerna som representeras i det här exemplet.
>
>

### <a name="failed-request-traces"></a>Det gick inte begäranden
Misslyckade begäranden lagras i XML-filer med namnet **fr ### .xml**. För att göra det enklare att visa information om loggade XSL-formatmallar med namnet **freb.xsl** finns i samma katalog som XML-filerna. Om du öppnar en XML-filerna i Internet Explorer använder Internet Explorer XSL-formatmallar för att tillhandahålla en formaterad visningen av spårningsinformation liknar följande exempel:

![misslyckade begäranden som visas i webbläsaren](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Detaljerade felloggar
Detaljerade felloggar är HTML-dokument som innehåller mer detaljerad information om HTTP-fel som har inträffat. Eftersom de bara HTML-dokument, kan de visas i en webbläsare.

### <a name="web-server-logs"></a>Webbserverloggar
Web server-loggar har formaterats med den [W3C utökat loggfilsformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Den här informationen kan läsas med hjälp av en textredigerare eller parsas med verktyg, till exempel [Loggparser](http://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Loggar som genereras av Azure-webbappar stöder inte den **s-computername**, **s-IP-**, eller **cs-version** fält.
>
>

## <a name="nextsteps"></a>Nästa steg
* [Så här övervakar du Web Apps](web-sites-monitor.md)
* [Felsöka Azure web apps i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
* [Analysera Webbprogramloggar i HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
>
>
