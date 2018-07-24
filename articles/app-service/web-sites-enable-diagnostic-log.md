---
title: Aktivera diagnostikloggning för webbappar i Azure App Service
description: Lär dig hur du aktiverar diagnostikloggning och lägger till instrumentering i programmet, samt hur du kommer åt den information som loggas av Azure.
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
ms.openlocfilehash: 0eefb8ecb7c9641b3e025054f54e2b7cf97b94bd
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206011"
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Aktivera diagnostikloggning för webbappar i Azure App Service
## <a name="overview"></a>Översikt
Azure har inbyggd diagnostik som hjälper till med felsökning av en [App Service-webbapp](http://go.microsoft.com/fwlink/?LinkId=529714). I den här artikeln lär du dig hur du aktiverar diagnostikloggning och lägger till instrumentering i programmet, samt hur du kommer åt den information som loggas av Azure.

Den här artikeln används den [Azure-portalen](https://portal.azure.com), Azure PowerShell och Azure-kommandoradsgränssnittet (Azure CLI) för att arbeta med diagnostikloggar. Information om hur du arbetar med diagnostikloggar med Visual Studio finns i [felsökning Azure i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Web serverdiagnostik- och programdiagnostik
App Service web apps ger diagnostisk funktionalitet för att logga information från både webbservern och webbprogrammet. Dessa logiskt är indelade i **web serverdiagnostik** och **programdiagnostik**.

### <a name="web-server-diagnostics"></a>Web serverdiagnostik
Du kan aktivera eller inaktivera följande typer av loggar:

* **Detaljerad felloggning** -detaljerad information om fel för HTTP-statuskoder som indikerar ett fel (statuskod 400 eller större). Den kan innehålla information som kan hjälpa dig att avgöra varför servern returnerade felkoden.
* **Det gick inte att begäran om spårning av** -detaljerad information om misslyckade förfrågningar, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och den tid det tar i varje komponent. Det är användbart om du vill öka platsprestanda eller isolera vad som orsakar ett specifikt HTTP-fel som ska returneras.
* **Web Server-loggning** -Information om HTTP-transaktioner med hjälp av den [W3C utökat loggfilsformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Det är användbart när du fastställer övergripande platsmått, till exempel antalet begäranden som hanteras eller hur många begäranden som kommer från en specifik IP-adress.

### <a name="application-diagnostics"></a>Programdiagnostik
Programdiagnostik kan du samla in information som genereras av ett webbprogram. ASP.NET-program kan använda den [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) klassen för att logga information till programloggen för diagnostik. Exempel:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Vid körning, kan du hämta dessa loggar för felsökning. Mer information finns i [felsöka Azure web apps i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

App Service web apps kan du också logga information om programdistribution när du publicerar innehåll till en webbapp. Det sker automatiskt och det finns inga konfigurationsinställningar för distribution av loggning. Distribution loggning kan du fastställa varför en distribution misslyckades. Om du använder ett anpassat distributionsskript kan du till exempel använda distribution loggning för att avgöra varför skriptet inte fungerar.

## <a name="enablediag"></a>Så här aktiverar du diagnostik
Aktivera diagnostik i den [Azure-portalen](https://portal.azure.com), gå till sidan för din webbapp och klicka på **Inställningar > diagnostikloggar**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![En del loggar](./media/web-sites-enable-diagnostic-log/logspart.png)

När du aktiverar **programdiagnostik**, du också välja den **nivå**. Den här inställningen kan du filtrera den information som hämtas till **endast i informationssyfte**, **varning**, eller **fel** information. Ange värdet till **utförlig** loggar all information som genereras av programmet.

> [!NOTE]
> Till skillnad från ändra filen web.config, aktiverar programdiagnostik eller ändrar diagnostiklogg nivåer inte att återanvända den app-domän som programmet körs inom.
>
>

För **programloggning**, du kan aktivera system filalternativet tillfälligt för felsökning. Det här alternativet inaktiverar automatiskt i 12 timmar. Du kan också aktivera alternativet blob för att välja en blobbehållare loggfiler ska skrivas i.

För **webbserverloggning**, kan du välja **storage** eller **filsystem**. Att välja **storage** kan du välja ett lagringskonto och en blob-behållare som loggarna skrivs till. 

Om du sparar loggar på filsystemet kan filerna nås via FTP eller hämtat som ett Zip-arkiv med hjälp av Azure PowerShell eller Azure-kommandoradsgränssnittet (Azure CLI).

Som standard loggar tas inte bort automatiskt (med undantag för **Programinloggning (filsystem)**). Om du vill ta bort loggar automatiskt, ange den **kvarhållningsperiod (dagar)** fält.

> [!NOTE]
> Om du [återskapa lagringskontots åtkomstnycklar](../storage/common/storage-create-storage-account.md), måste du återställa respektive loggningsinställningarna för att använda de uppdaterade nycklarna. Gör så här:
>
> 1. I den **konfigurera** fliken genom att ange respektive loggningsfunktionen **av**. Spara dina inställningar.
> 2. Aktivera loggning för att logglagringskontots blob eller table igen. Spara dina inställningar.
>
>

Valfri kombination av filsystemet, tabellagring eller blob storage kan aktiveras på samma gång och har enskild logg på konfigurationer. Du kanske exempelvis vill logga fel och varningar till blob storage som en långsiktig loggning lösning vid aktivering av loggning av fil-system med en nivå av utförlig.

När alla tre lagringsplatser ger samma grundläggande information för loggade händelser **tabellagring** och **blob-lagring** logga ytterligare information, till exempel instans-ID, tråd-ID och en mer Detaljerad tidsstämpel (Ticket-format) än loggning till **filsystem**.

> [!NOTE]
> Information som lagras i **tabellagring** eller **blob-lagring** kan bara användas med ett storage-klienten eller ett program som kan arbeta direkt med dessa lagringssystem. Till exempel Visual Studio 2013 innehåller en lagringsutforskare som kan användas för att utforska tabell eller blob storage och HDInsight kan komma åt data som lagras i blob storage. Du kan också skriva ett program som ansluter till Azure Storage med hjälp av en av de [Azure SDK: er](https://azure.microsoft.com/en-us/downloads/).
>
> [!NOTE]
> Diagnostik kan även aktiveras från Azure PowerShell med hjälp av den **Set-AzureWebsite** cmdlet. Om du inte har installerat Azure PowerShell eller inte har konfigurerat den att använda din Azure-prenumeration, se [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0).
>
>

## <a name="download"></a> Så här: ladda ned loggar
Diagnostisk information som lagras i web app-filsystem kan nås direkt med FTP. Det kan också hämtas som en Zip-arkiv med Azure PowerShell eller Azure-kommandoradsgränssnittet.

Katalogstrukturen som loggar lagras i är följande:

* **Programloggar** -/LogFiles/program /. Den här mappen innehåller en eller flera textfiler som innehåller information som genereras av programloggning.
* **Det gick inte begäran spårningar** -/ LogFiles/W3SVC ### /. Den här mappen innehåller en XSL-fil och en eller flera XML-filer. Se till att du hämtar XSL-fil i samma katalog som XML-filer eftersom XSL-filen innehåller funktioner för att formatera och filtrera innehållet i XML-filen när den visas i Internet Explorer.
* **Detaljerade felloggar** -/LogFiles/DetailedErrors /. Den här mappen innehåller en eller flera .htm-filer som innehåller omfattande information för alla HTTP-fel som har inträffat.
* **Web Server-loggar** -/LogFiles/http/RawLogs. Den här mappen innehåller en eller flera textfiler formaterade med hjälp av den [W3C utökat loggfilsformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Distributionsloggar** -/ LogFiles/Git. Den här mappen innehåller loggar som genereras av de interna distributionsprocesser som används av Azure-webbappar samt loggar för Git-distributioner. Du kan också hitta distributionsloggar under D:\home\site\deployments.

### <a name="ftp"></a>FTP

Om du vill öppna en FTP-anslutning till FTP-server för din app Se [distribuera din app till Azure App Service med FTP/S](app-service-deploy-ftp.md).

När du är ansluten till din webbapps FTP/S-servern, öppna den **LogFiles** mappen där loggfilerna lagras.

### <a name="download-with-azure-powershell"></a>Ladda ned med Azure PowerShell
Starta en ny instans av Azure PowerShell för att hämta filerna, och använder du följande kommando:

    Save-AzureWebSiteLog -Name webappname

Det här kommandot sparar loggarna för den webbapp som anges av den **-namnet** parametern till en fil med namnet **logs.zip** i den aktuella katalogen.

> [!NOTE]
> Om du inte har installerat Azure PowerShell eller inte har konfigurerat den att använda din Azure-prenumeration, se [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0).
>
>

### <a name="download-with-azure-command-line-interface"></a>Ladda ned Azure kommandoradsgränssnitt
För att hämta filerna med hjälp av Azure-kommandoradsgränssnittet, öppna en kommandotolk, PowerShell, Bash eller terminalsession och ange följande kommando:

    az webapp log download --resource-group resourcegroupname --name webappname

Det här kommandot sparar loggar för en webbapp med namnet webappname om du till en fil med namnet **diagnostics.zip** i den aktuella katalogen.

> [!NOTE]
> Om du inte har installerat Azure-kommandoradsgränssnittet (Azure CLI) eller inte har konfigurerat den att använda din Azure-prenumeration, se [hur du använder Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Så här: visa loggar i Application Insights
Visual Studio Application Insights innehåller verktyg för att filtrera och söka i loggar och för att korrelera loggarna med begäranden och andra händelser.

1. Lägg till Application Insights SDK till ditt projekt i Visual Studio.
   * Högerklicka på projektet i Solution Explorer och välj Lägg till Application Insights. Gränssnittet vägleder dig genom stegen som omfattar att skapa en Application Insights-resurs. [Läs mer](../application-insights/app-insights-asp-net.md)
2. Lägg till Trace Listener-paketet i projektet.
   * Högerklicka på projektet och välj Hantera NuGet-paket. Välj `Microsoft.ApplicationInsights.TraceListener` [Läs mer](../application-insights/app-insights-asp-net-trace-logs.md)
3. Ladda upp ditt projekt och kör den och generera loggdata.
4. I den [Azure-portalen](https://portal.azure.com/)bläddrar du till nya Application Insights-resursen och öppna **Search**. Du bör se loggdata, tillsammans med begäran, användning och annan telemetri. Telemetri kan ta några minuter att tas emot: Klicka på Uppdatera. [Läs mer](../application-insights/app-insights-diagnostic-search.md)

[Mer information om prestandaspårning med Application Insights](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a> Så här: Stream loggar
När du utvecklar ett program, kan det ofta vara användbart att visa loggningsinformation i nära realtid. Du kan strömma loggningsinformation till utvecklingsmiljön med Azure PowerShell eller Azure-kommandoradsgränssnittet.

> [!NOTE]
> Vissa typer av loggning bufferten skriver till loggfilen, vilket kan resultera i oordnade händelser i strömmen. Exempelvis visas en loggpost för program som uppstår när en användare besöker en sida i strömmen innan den motsvarande HTTP-loggfil för sidan-begäran.
>
> [!NOTE]
> Loggströmningen också strömmar information skrivs till alla textfiler som lagras i den **D:\\home\\LogFiles\\**  mapp.
>
>

### <a name="streaming-with-azure-powershell"></a>Strömning med Azure PowerShell
Att strömma loggningsinformation, starta en ny instans av Azure PowerShell och Använd följande kommando:

    Get-AzureWebSiteLog -Name webappname -Tail

Det här kommandot ansluter till den webbapp som anges av den **-namnet** parametern och påbörja direktuppspelning information till PowerShell-fönstret när händelser inträffar i webbappen. All information som skrivs till filer som slutar på .txt, .log eller .htm som lagras i katalogen /LogFiles (d:/home/logfiles) strömmas till den lokala konsolen.

För att filtrera specifika händelser, t.ex fel som använder den **-meddelandet** parametern. Exempel:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

För att filtrera specifika loggtyper, till exempel HTTP, använder den **-sökvägen** parametern. Exempel:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Använd parametern - ListPath om du vill se en lista över tillgängliga sökvägar.

> [!NOTE]
> Om du inte har installerat Azure PowerShell eller inte har konfigurerat den att använda din Azure-prenumeration, se [hur du använder Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="streaming-with-azure-command-line-interface"></a>Strömning med Azure-kommandoradsgränssnittet
Strömma loggningsinformation, öppna en kommandotolk, PowerShell, Bash eller terminalsession och ange följande kommando:

    az webapp log tail --name webappname --resource-group myResourceGroup

Det här kommandot ansluter till en webbapp med namnet 'webappname' och påbörja direktuppspelning information till fönstret när händelser inträffar i webbappen. All information som skrivs till filer som slutar på .txt, .log eller .htm som lagras i katalogen /LogFiles (d:/home/logfiles) strömmas till den lokala konsolen.

För att filtrera specifika händelser, t.ex fel som använder den **--Filter** parametern. Exempel:

    az webapp log tail --name webappname --resource-group myResourceGroup --filter Error

För att filtrera specifika loggtyper, till exempel HTTP, använder den **--sökväg** parametern. Exempel:

    az webapp log tail --name webappname --resource-group myResourceGroup --path http

> [!NOTE]
> Om du inte har installerat Azure-kommandoradsgränssnittet eller inte har konfigurerat den att använda din Azure-prenumeration, se [hur du använder Azure-kommandoradsgränssnittet](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Så här: Förstå diagnostikloggar
### <a name="application-diagnostics-logs"></a>Program-diagnostikloggar
Programdiagnostik lagrar information i ett visst format för .NET-program, beroende på om du vill lagra loggarna till filsystemet, tabellagring eller blob-lagring. Den grundläggande uppsättningen med data som lagras är samma för alla tre lagringstyper - datum och tid som händelsen inträffade, process-ID som producerade händelsen, händelsetyp (information, varning, fel) och händelsemeddelandet.

**Filsystem**

Varje rad loggas till filsystemet eller tas emot med direktuppspelning är i följande format:

    {Date}  PID[{process ID}] {event type/level} {message}

Till exempel visas en felhändelse liknar följande exempel:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Loggning till filsystemet innehåller den mest grundläggande informationen av tre tillgängliga metoder ger endast tid, process-ID, Händelsenivå och meddelandet.

**Table Storage**

När du loggar till tabellagring används ytterligare egenskaper som för att underlätta söka data som lagras i tabellen samt mer detaljerad information om händelsen. Följande egenskaper (kolumner) används för varje entitet (rad) som lagras i tabellen.

| Egenskapsnamn | Värdeformat / |
| --- | --- |
| PartitionKey |Datum/tid då händelsen i yyyyMMddHH format |
| RowKey |Ett GUID-värde som unikt identifierar den här entiteten |
| Tidsstämpel |Datum och tid då händelsen inträffade |
| EventTickCount |Datum och tid då händelsen inträffade i Skalstreckets format (större precision) |
| ApplicationName |Webbappens namn |
| Nivå |Händelsenivå (till exempel fel, varning, information) |
| EventId |Händelse-ID för den här händelsen<p><p>Standardvärdet är 0 om inget anges |
| Instans-ID |Instans av webbappen som den händelse som inträffat på |
| Process-ID |Process-ID |
| tid |Tråd-ID för tråden som producerade händelsen |
| Meddelande |Detalj händelsemeddelande |

**Blob Storage**

När du loggar till blob storage lagras data i fil med kommaavgränsade värden (CSV)-format. Liknar tabellagring, ytterligare fält som loggas för att tillhandahålla mer detaljerad information om händelsen. Följande egenskaper som används för varje rad i CSV-filen:

| Egenskapsnamn | Värdeformat / |
| --- | --- |
| Date |Datum och tid då händelsen inträffade |
| Nivå |Händelsenivå (till exempel fel, varning, information) |
| ApplicationName |Webbappens namn |
| Instans-ID |Instans av webbappen som händelsen inträffade på |
| EventTickCount |Datum och tid då händelsen inträffade i Skalstreckets format (större precision) |
| EventId |Händelse-ID för den här händelsen<p><p>Standardvärdet är 0 om inget anges |
| Process-ID |Process-ID |
| tid |Tråd-ID för tråden som producerade händelsen |
| Meddelande |Detalj händelsemeddelande |

Data som lagras i en blob skulle se ut ungefär så här:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Den första raden i loggen innehåller kolumnrubriker som representeras i det här exemplet.
>
>

### <a name="failed-request-traces"></a>Det gick inte begäran spårningar
Spårning av misslyckade begäranden lagras i XML-filer med namnet **fr ### .xml**. Om du vill göra det enklare att visa informationen som loggas, en XSL-formatmall med namnet **freb.xsl** finns i samma katalog som XML-filerna. Om du öppnar en XML-filerna i Internet Explorer, använder Internet Explorer XSL-formatmall för att tillhandahålla en formaterad visningen av spårningsinformationen som liknar följande exempel:

![misslyckade begäranden som visas i webbläsaren](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Detaljerade felloggar
Detaljerade felloggar är HTML-dokument som innehåller mer detaljerad information om HTTP-fel som har inträffat. Eftersom de är helt enkelt HTML-dokument, kan de granskas med en webbläsare.

### <a name="web-server-logs"></a>Webbserverloggar
Webbserverloggarna är formaterade med hjälp av den [W3C utökat loggfilsformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Den här informationen kan läsas med hjälp av en textredigerare eller parsas med verktyg som [Loggparser](http://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Loggar som producerats av webbappar i Azure stöder inte den **s-computername**, **s-ip**, eller **cs-version** fält.
>
>

## <a name="nextsteps"></a>Nästa steg
* [Övervaka Webbappar](web-sites-monitor.md)
* [Felsöka Azure web apps i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
* [Analysera Webbprogramloggar i HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
>
>
