---
title: Aktivera diagnostikloggning för appar – Azure App Service
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
ms.custom: seodec18
ms.openlocfilehash: 37455c278d665d05636ec120ca91b76153e53d16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835727"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Aktivera diagnostikloggning för appar i Azure App Service
## <a name="overview"></a>Översikt
Azure har inbyggd diagnostik som hjälper till med felsökning av en [App Service-app](https://go.microsoft.com/fwlink/?LinkId=529714). I den här artikeln lär du dig hur du aktiverar diagnostikloggning och lägger till instrumentering i programmet, samt hur du kommer åt den information som loggas av Azure.

Den här artikeln används den [Azure-portalen](https://portal.azure.com) och Azure CLI för att arbeta med diagnostikloggar. Information om hur du arbetar med diagnostikloggar med Visual Studio finns i [felsökning Azure i Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Web serverdiagnostik- och programdiagnostik
App Service tillhandahåller diagnostisk funktionalitet för att logga information från både webbservern och webbprogrammet. Dessa logiskt är indelade i **web serverdiagnostik** och **programdiagnostik**.

### <a name="web-server-diagnostics"></a>Web serverdiagnostik
Du kan aktivera eller inaktivera följande typer av loggar:

* **Detaljerad felloggning** -detaljerad information för varje begäran som resulterar i HTTP-statuskod 400 eller större. Den kan innehålla information som kan hjälpa dig att avgöra varför servern returnerade felkoden. En HTML-fil skapas för varje fel i appens filsystemet och upp till 50 fel (filer) bevaras. När antalet HTML-filer överskrider 50 är tas de äldsta 26 filerna bort automatiskt.
* **Det gick inte att begäran om spårning av** -detaljerad information om misslyckade förfrågningar, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och den tid det tar i varje komponent. Det är användbart om du vill förbättra webbplatsernas prestanda eller isolera ett specifikt HTTP-fel. En mapp skapas för varje fel i appens filsystem. Principer för kvarhållning av filer är samma som detaljerad felloggning ovan.
* **Web Server-loggning** -Information om HTTP-transaktioner med hjälp av den [W3C utökat loggfilsformat](/windows/desktop/Http/w3c-logging). Det är användbart när du fastställer övergripande platsmått, till exempel antalet begäranden som hanteras eller hur många begäranden som kommer från en specifik IP-adress.

### <a name="application-diagnostics"></a>Programdiagnostik
Programdiagnostik kan du samla in information som genereras av ett webbprogram. ASP.NET-program kan använda den [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) klassen för att logga information till programloggen för diagnostik. Exempel:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Vid körning, kan du hämta dessa loggar för felsökning. Mer information finns i [felsökning av Azure App Service i Visual Studio](troubleshoot-dotnet-visual-studio.md).

App Service loggar också information om programdistribution när du publicerar innehåll till en app. Det sker automatiskt och det finns inga konfigurationsinställningar för distribution av loggning. Distribution loggning kan du fastställa varför en distribution misslyckades. Om du använder ett anpassat distributionsskript kan du till exempel använda distribution loggning för att avgöra varför skriptet inte fungerar.

## <a name="enablediag"></a>Så här aktiverar du diagnostik
Aktivera diagnostik i den [Azure-portalen](https://portal.azure.com), gå till sidan för din app och klicka på **Inställningar > diagnostikloggar**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![En del loggar](./media/web-sites-enable-diagnostic-log/logspart.png)

När du aktiverar **programdiagnostik**, du också välja den **nivå**. I följande tabell visas olika kategorier av loggar varje nivå innehåller:

| Nivå| Inkluderade loggkategorier |
|-|-|
|**Inaktiverad** | Ingen |
|**Fel** | Fel, kritiska |
|**Varning** | Varning, kritiskt fel|
|**Information** | Info, varning, kritiskt fel|
|**utförlig** | Spårning, Avbugga, Info, varning, fel, kritiskt (alla kategorier) |
|-|-|

För **programloggning**, du kan aktivera system filalternativet tillfälligt för felsökning. Det här alternativet inaktiverar automatiskt i 12 timmar. Du kan också aktivera alternativet blob för att välja en blobbehållare loggfiler ska skrivas i.

> [!NOTE]
> För närvarande endast .NET programloggarna kan skrivas till blob storage. Java, PHP, Node.js, Python programloggar bara kan sparas i filsystemet (utan kodändringar loggfiler ska skrivas till extern lagring).
>
>

För **webbserverloggning**, kan du välja **storage** eller **filsystem**. Att välja **storage** kan du välja ett lagringskonto och en blob-behållare som loggarna skrivs till. 

Om du sparar loggar på filsystemet kan filerna nås via FTP eller hämtat som ett Zip-arkiv med hjälp av Azure CLI.

Som standard loggar tas inte bort automatiskt (med undantag för **Programinloggning (filsystem)**). Om du vill ta bort loggar automatiskt, ange den **kvarhållningsperiod (dagar)** fält.

> [!NOTE]
> Om du [återskapa lagringskontots åtkomstnycklar](../storage/common/storage-create-storage-account.md), måste du återställa respektive loggningsinställningarna för att använda de uppdaterade nycklarna. Gör så här:
>
> 1. I den **konfigurera** fliken genom att ange respektive loggningsfunktionen **av**. Spara dina inställningar.
> 2. Aktivera loggning för blob storage-kontot igen. Spara dina inställningar.
>
>

Valfri kombination av file system eller blob storage kan aktiveras på samma gång och har enskild logg på konfigurationer. Du kanske exempelvis vill logga fel och varningar till blob storage som en långsiktig loggning lösning vid aktivering av loggning av fil-system med en nivå av utförlig.

Även om båda lagringsplatser ger samma grundläggande information för loggade händelser **blob-lagring** loggar ytterligare information, till exempel instans-ID, tråd-ID och en mer detaljerad tidsstämpel (Ticket-format) än loggning till **filsystem**.

> [!NOTE]
> Information som lagras i **blob-lagring** kan bara användas med ett storage-klienten eller ett program som kan arbeta direkt med dessa lagringssystem. Till exempel Visual Studio 2013 innehåller en lagringsutforskare som kan användas för att utforska blob-lagring och HDInsight kan komma åt data som lagras i blob storage. Du kan också skriva ett program som ansluter till Azure Storage med hjälp av en av de [Azure SDK: er](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a> Hur: Hämtningsloggar
Diagnostisk information som lagras i app-filsystem kan nås direkt med FTP. Det kan också hämtas som en Zip-arkiv med Azure CLI.

Katalogstrukturen som loggar lagras i är följande:

* **Programloggar** -/LogFiles/program /. Den här mappen innehåller en eller flera textfiler som innehåller information som genereras av programloggning.
* **Det gick inte begäran spårningar** -/ LogFiles/W3SVC ### /. Den här mappen innehåller en XSL-fil och en eller flera XML-filer. Se till att du hämtar XSL-fil i samma katalog som XML-filer eftersom XSL-filen innehåller funktioner för att formatera och filtrera innehållet i XML-filen när den visas i Internet Explorer.
* **Detaljerade felloggar** -/LogFiles/DetailedErrors /. Den här mappen innehåller en eller flera .htm-filer som innehåller omfattande information för alla HTTP-fel som har inträffat.
* **Web Server-loggar** -/LogFiles/http/RawLogs. Den här mappen innehåller en eller flera textfiler formaterade med hjälp av den [W3C utökat loggfilsformat](/windows/desktop/Http/w3c-logging).
* **Distributionsloggar** -/ LogFiles/Git. Den här mappen innehåller loggar som genereras av de interna distributionsprocesser som används av Azure App Service samt loggar för Git-distributioner. Du kan också hitta distributionsloggar under D:\home\site\deployments.

### <a name="ftp"></a>FTP

Om du vill öppna en FTP-anslutning till FTP-server för din app Se [distribuera din app till Azure App Service med FTP/S](deploy-ftp.md).

När du är ansluten till din app FTP/S-servern, öppna den **LogFiles** mappen där loggfilerna lagras.

### <a name="download-with-azure-cli"></a>Ladda ned med Azure CLI
För att hämta filerna med hjälp av Azure-kommandoradsgränssnittet, öppna en kommandotolk, PowerShell, Bash eller terminalsession och ange följande kommando:

    az webapp log download --resource-group resourcegroupname --name appname

Det här kommandot sparar loggar för appen med namnet ”appname” till en fil med namnet **webapp_logs.zip** i den aktuella katalogen.

> [!NOTE]
> Om du inte har installerat Azure CLI eller inte har konfigurerat den att använda din Azure-prenumeration, se [hur du använder Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Anvisningar: Visa loggar i Application Insights
Visual Studio Application Insights innehåller verktyg för att filtrera och söka i loggar och för att korrelera loggarna med begäranden och andra händelser.

1. Lägg till Application Insights SDK till ditt projekt i Visual Studio.
   * Högerklicka på projektet i Solution Explorer och välj Lägg till Application Insights. Gränssnittet vägleder dig genom stegen som omfattar att skapa en Application Insights-resurs. [Läs mer](../azure-monitor/app/asp-net.md)
2. Lägg till Trace Listener-paketet i projektet.
   * Högerklicka på projektet och välj Hantera NuGet-paket. Välj `Microsoft.ApplicationInsights.TraceListener` [Läs mer](../azure-monitor/app/asp-net-trace-logs.md)
3. Ladda upp ditt projekt och kör den och generera loggdata.
4. I den [Azure-portalen](https://portal.azure.com/)bläddrar du till nya Application Insights-resursen och öppna **Search**. Du bör se loggdata, tillsammans med begäran, användning och annan telemetri. Telemetri kan ta några minuter att tas emot: Klicka på Uppdatera. [Läs mer](../azure-monitor/app/diagnostic-search.md)

[Mer information om prestandaspårning med Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a> Hur: Strömningsloggar
När du utvecklar ett program, kan det ofta vara användbart att visa loggningsinformation i nära realtid. Du kan strömma loggningsinformation till utvecklingsmiljön med Azure CLI.

> [!NOTE]
> Vissa typer av loggning bufferten skriver till loggfilen, vilket kan resultera i oordnade händelser i strömmen. Exempelvis visas en loggpost för program som uppstår när en användare besöker en sida i strömmen innan den motsvarande HTTP-loggfil för sidan-begäran.
>
> [!NOTE]
> Loggströmningen också strömmar information skrivs till alla textfiler som lagras i den **D:\\home\\LogFiles\\**  mapp.
>
>

### <a name="streaming-with-azure-cli"></a>Strömning med Azure CLI
Strömma loggningsinformation, öppna en kommandotolk, PowerShell, Bash eller terminalsession och ange följande kommando:

    az webapp log tail --name appname --resource-group myResourceGroup

Det här kommandot ansluter till appen med namnet ”appname” och påbörja direktuppspelning information till fönstret när händelser inträffar i appen. All information som skrivs till filer som slutar på .txt, .log eller .htm som lagras i katalogen /LogFiles (d:/home/logfiles) strömmas till den lokala konsolen.

För att filtrera specifika händelser, t.ex fel som använder den **--Filter** parametern. Exempel:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

För att filtrera specifika loggtyper, till exempel HTTP, använder den **--sökväg** parametern. Exempel:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Om du inte har installerat Azure CLI eller inte har konfigurerat den att använda din Azure-prenumeration, se [hur du använder Azure CLI](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Hur: Förstå diagnostikloggar
### <a name="application-diagnostics-logs"></a>Program-diagnostikloggar
Programdiagnostik lagrar information i ett visst format för .NET-program, beroende på om du vill lagra loggarna till file system- eller blob storage. 

Den grundläggande uppsättningen med data som lagras är samma för både lagringstyper - datum och tid som händelsen inträffade, process-ID som producerade händelsen, händelsetyp (information, varning, fel) och händelsemeddelandet. Det är praktiskt att använda filsystemet för logglagringsutrymme när du behöver omedelbar åtkomst till att felsöka ett problem Eftersom loggfilerna har uppdaterats nästan omedelbart. BLOB storage används för arkivering eftersom filerna cachelagras och sedan rensade lagringsbehållare enligt ett schema.

**Filsystem**

Varje rad loggas till filsystemet eller tas emot med direktuppspelning är i följande format:

    {Date}  PID[{process ID}] {event type/level} {message}

Till exempel visas en felhändelse liknar följande exempel:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Loggning till filsystemet innehåller den mest grundläggande informationen av tre tillgängliga metoder ger endast tid, process-ID, Händelsenivå och meddelandet.

**Blob Storage**

När du loggar till blob storage lagras data i fil med kommaavgränsade värden (CSV)-format. Ytterligare fält loggas för att ge mer detaljerad information om händelsen. Följande egenskaper som används för varje rad i CSV-filen:

| Egenskapsnamn | Värdeformat / |
| --- | --- |
| Date |Datum och tid då händelsen inträffade |
| Nivå |Händelsenivå (till exempel fel, varning, information) |
| ApplicationName |Appnamnet |
| Instans-ID |Instans av appen som händelsen inträffade på |
| EventTickCount |Datum och tid då händelsen inträffade i Skalstreckets format (större precision) |
| EventId |Händelse-ID för den här händelsen<p><p>Standardvärdet är 0 om inget anges |
| Pid |Process-ID |
| tid |Tråd-ID för tråden som producerade händelsen |
| Meddelande |Detalj händelsemeddelande |

Data som lagras i en blob skulle se ut ungefär så här:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> För ASP.NET Core loggning åstadkoms med hjälp av den [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider som den här providern insättningar ytterligare loggfiler till blob-behållaren. Mer information finns i [ASP.NET Core loggning i Azure](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure).
>
>

### <a name="failed-request-traces"></a>Det gick inte begäran spårningar
Spårning av misslyckade begäranden lagras i XML-filer med namnet **fr ### .xml**. Om du vill göra det enklare att visa informationen som loggas, en XSL-formatmall med namnet **freb.xsl** finns i samma katalog som XML-filerna. Om du öppnar en XML-filerna i Internet Explorer, använder Internet Explorer XSL-formatmall för att tillhandahålla en formaterad visningen av spårningsinformationen som liknar följande exempel:

![misslyckade begäranden som visas i webbläsaren](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> Ett enkelt sätt att visa formaterade misslyckade begäranden spårningar är att gå till appens sida i portalen. I den vänstra menyn, Välj **diagnostisera och lösa problem**, Sök sedan efter **det gick inte begära spårningsloggar**, klickar på ikonen för att bläddra och visa spårning som du vill.
>

### <a name="detailed-error-logs"></a>Detaljerade felloggar
Detaljerade felloggar är HTML-dokument som innehåller mer detaljerad information om HTTP-fel som har inträffat. Eftersom de är helt enkelt HTML-dokument, kan de granskas med en webbläsare.

### <a name="web-server-logs"></a>Webbserverloggar
Webbserverloggarna är formaterade med hjälp av den [W3C utökat loggfilsformat](/windows/desktop/Http/w3c-logging). Den här informationen kan läsas med hjälp av en textredigerare eller parsas med verktyg som [Loggparser](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Loggar som producerats av Azure App Service stöder inte den **s-computername**, **s-ip**, eller **cs-version** fält.
>
>

## <a name="nextsteps"></a>Nästa steg
* [Så här övervakar du Azure App Service](web-sites-monitor.md)
* [Felsöka Azure App Service i Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analysera Apploggar i HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
