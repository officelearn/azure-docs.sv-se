---
title: Aktivera diagnostikloggning för appar – Azure App Service
description: Lär dig hur du aktiverar diagnostikloggning och lägger till instrumentering i programmet, samt hur du får åtkomst till den information som loggas av Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: af6d8b61c5d49ae219e90513abb93185f957222e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074063"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Aktivera diagnostikloggning för appar i Azure App Service
## <a name="overview"></a>Översikt
Azure har inbyggd diagnostik som hjälper till med fel sökning av en [App Service app](https://go.microsoft.com/fwlink/?LinkId=529714). I den här artikeln får du lära dig hur du aktiverar diagnostikloggning och lägger till instrumentering i programmet, samt hur du får åtkomst till den information som loggas av Azure.

I den här artikeln används [Azure Portal](https://portal.azure.com) och Azure CLI för att arbeta med diagnostikloggar. Information om hur du arbetar med diagnostiska loggar med Visual Studio finns i [Felsöka Azure i Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Diagnostik för webb server och Programdiagnostik
App Service innehåller diagnostiska funktioner för loggning av information från både webb servern och webb programmet. De är logiskt åtskilda i **Web Server Diagnostics** och **Application Diagnostics**.

### <a name="web-server-diagnostics"></a>Diagnostik för webb server
Du kan aktivera eller inaktivera följande typer av loggar:

* **Detaljerad fel loggning** – detaljerad information för alla förfrågningar som resulterar i HTTP-statuskod 400 eller senare. Den kan innehålla information som kan hjälpa dig att avgöra varför servern returnerade felkoden. En HTML-fil skapas för varje fel i appens fil system, och upp till 50 fel (filer) bevaras. När antalet HTML-filer överstiger 50 tas de äldsta 26 filerna bort automatiskt.
* **Spårning av misslyckade begär** Anden-detaljerad information om misslyckade förfrågningar, inklusive spårning av IIS-komponenter som används för att bearbeta begäran och den tid som tagits i varje komponent. Det är användbart om du vill förbättra platsens prestanda eller isolera ett särskilt HTTP-fel. En mapp skapas för varje fel i appens fil system. Principerna för fil lagring är desamma som för den detaljerade fel loggningen ovan.
* **Webb Server loggning** – information om http-transaktioner med [utökat logg fils format för W3C](/windows/desktop/Http/w3c-logging). Det är användbart när du fastställer allmänna webbplats mått, till exempel antalet begär Anden som hanteras eller hur många begär Anden som kommer från en speciell IP-adress.

### <a name="application-diagnostics"></a>Programdiagnostik
Med Application Diagnostics kan du samla in information som produceras av ett webb program. ASP.NET-program kan använda klassen [system. Diagnostics. trace](/dotnet/api/system.diagnostics.trace) för att logga information i Application Diagnostics-loggen. Exempel:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Vid körning kan du hämta loggarna för att under lätta fel sökningen. Mer information finns i [felsöka Azure App Service i Visual Studio](troubleshoot-dotnet-visual-studio.md).

App Service loggar även distributions information när du publicerar innehåll till en app. Det sker automatiskt och det finns inga konfigurations inställningar för distributions loggning. Med distributions loggning kan du avgöra varför en distribution misslyckades. Om du till exempel använder ett anpassat distributions skript kan du använda distributions loggning för att avgöra varför skriptet Miss söker.

## <a name="enablediag"></a>Aktivera diagnostik
Om du vill aktivera diagnostik i [Azure Portal](https://portal.azure.com)går du till sidan för appen och klickar på **Inställningar >** diagnostikloggar.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Delen loggar](./media/web-sites-enable-diagnostic-log/logspart.png)

När du aktiverar **Application Diagnostics**väljer du även **nivå**. I följande tabell visas de kategorier av loggar som varje nivå omfattar:

| Nivå| Inkluderade logg kategorier |
|-|-|
|**Inaktiverad** | Inga |
|**Fels** | Fel, kritiskt |
|**Honom** | Varning, fel, kritiskt|
|**Mer** | Information, varning, fel, kritiskt|
|**Utförlig** | Spåra, felsöka, info, varning, fel, kritiskt (alla kategorier) |
|-|-|

För **program loggning**kan du aktivera fil system alternativet tillfälligt för fel sökning. Det här alternativet stängs av automatiskt om 12 timmar. Du kan också aktivera alternativet Blob Storage för att välja en BLOB-behållare att skriva loggar till.

> [!NOTE]
> För närvarande kan endast .NET-program loggar skrivas till blob-lagringen. Java, PHP, Node. js, python-programloggarna kan bara lagras i fil systemet (utan kod ändringar för att skriva loggar till extern lagring).
>
>

För **webb Server loggning**kan du välja **lagrings** -eller **fil system**. Genom att välja **lagring** kan du välja ett lagrings konto och sedan en BLOB-behållare som loggarna skrivs till. 

Om du lagrar loggar i fil systemet kan filerna nås via FTP eller laddas ned som ett zip-arkiv med hjälp av Azure CLI.

Som standard tas loggar inte bort automatiskt (med undantag för **program loggning (FileSystem)** ). Om du vill ta bort loggar automatiskt ställer du in fältet **kvarhållningsperiod (dagar)** .

> [!NOTE]
> Om du [återskapar åtkomst nycklarna för lagrings kontot](../storage/common/storage-create-storage-account.md)måste du återställa respektive loggnings konfiguration för att använda de uppdaterade nycklarna. Gör så här:
>
> 1. På fliken **Konfigurera** ställer du in respektive loggnings funktion på **av**. Spara inställningen.
> 2. Aktivera loggning till lagrings kontots BLOB igen. Spara inställningen.
>
>

Valfri kombination av fil system eller Blob-lagring kan aktive ras samtidigt, och ha individuella konfigurationer för loggnings nivå. Du kanske till exempel vill logga fel och varningar till Blob Storage som en långsiktig loggnings lösning, samtidigt som du aktiverar fil system loggning med en nivå av utförlig.

Även om båda lagrings platserna ger samma grundläggande information för loggade händelser loggar **Blob Storage** ytterligare information, till exempel instans-ID, tråd-ID och en mer detaljerad tidsstämpel (Ticket) än att logga till **fil systemet**.

> [!NOTE]
> Information som lagras i **Blob Storage** kan bara nås med hjälp av en lagrings klient eller ett program som kan fungera direkt med dessa lagrings system. Visual Studio 2013 innehåller till exempel ett Storage Explorer som kan användas för att utforska Blob Storage, och HDInsight kan komma åt data som lagras i Blob Storage. Du kan också skriva ett program som har åtkomst till Azure Storage med hjälp av en av [Azure SDK: erna](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a>Hur: Hämtningsloggar
Diagnostikinformation som lagras i app File System kan nås direkt via FTP. Den kan också hämtas som ett zip-arkiv med Azure CLI.

Katalog strukturen som loggarna lagras i är följande:

* **Program loggar** –/LogFiles/Application/. Den här mappen innehåller en eller flera textfiler som innehåller information som genereras av program loggningen.
* **Spårning av misslyckade begär Anden** -/LogFiles/W3SVC # # # # #/. Den här mappen innehåller en XSL-fil och en eller flera XML-filer. Se till att du laddar ned XSL-filen till samma katalog som XML-fil (er) eftersom XSL-filen innehåller funktioner för formatering och filtrering av innehållet i XML-filen (-erna) som visas i Internet Explorer.
* **Detaljerade fel loggar** –/LogFiles/DetailedErrors/. Den här mappen innehåller en eller flera. htm-filer som ger omfattande information om alla HTTP-fel som har inträffat.
* **Webb server loggar** –/LogFiles/http/RawLogs. Den här mappen innehåller en eller flera textfiler som formaterats med [utökat logg fils format för W3C](/windows/desktop/Http/w3c-logging).
* **Distributions loggar** –/LogFiles/git. Den här mappen innehåller loggar som skapats av de interna distributions processerna som används av Azure App Service, samt loggar för git-distributioner. Du kan också hitta distributions loggar under D:\home\site\deployments.

### <a name="ftp"></a>FTP

Information om hur du öppnar en FTP-anslutning till appens FTP-Server finns i [distribuera din app för att Azure App Service använda FTP/s](deploy-ftp.md).

När du är ansluten till appens FTP/S-server öppnar du mappen loggfiler där loggfilerna lagras.

### <a name="download-with-azure-cli"></a>Hämta med Azure CLI
Öppna en ny kommando tolk, PowerShell, bash eller terminalsession och ange följande kommando för att ladda ned loggfiler med Azures kommando rads gränssnitt:

    az webapp log download --resource-group resourcegroupname --name appname

Det här kommandot sparar loggarna för appen med namnet "APPNAME" i en fil med namnet **webapp_logs. zip** i den aktuella katalogen.

> [!NOTE]
> Om du inte har installerat Azure CLI eller inte har konfigurerat det för att använda din Azure-prenumeration, se [hur du använder Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Anvisningar: Visa loggar i Application Insights
Visual Studio Application Insights innehåller verktyg för att filtrera och söka efter loggar och för att korrelera loggarna med begär Anden och andra händelser.

1. Lägg till Application Insights SDK i projektet i Visual Studio.
   * I Solution Explorer högerklickar du på projektet och väljer Lägg till Application Insights. Gränssnittet vägleder dig genom steg som inkluderar att skapa en Application Insights-resurs. [Läs mer](../azure-monitor/app/asp-net.md)
2. Lägg till spårnings lyssnar paketet i projektet.
   * Högerklicka på projektet och välj Hantera NuGet-paket. Välj `Microsoft.ApplicationInsights.TraceListener` [Läs mer](../azure-monitor/app/asp-net-trace-logs.md)
3. Ladda upp projektet och kör det för att generera loggdata.
4. I [Azure Portal](https://portal.azure.com/)bläddrar du till den nya Application Insights resursen och öppnar **Sök**. Du bör se dina loggdata, tillsammans med begäran, användning och annan telemetri. Det kan ta några minuter att komma åt en viss telemetri: Klicka på Uppdatera. [Läs mer](../azure-monitor/app/diagnostic-search.md)

[Lär dig mer om prestanda spårning med Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a>Hur: Strömningsloggar
När du utvecklar ett program är det ofta användbart att se loggnings information i nära real tid. Du kan strömma loggnings information till utvecklings miljön med hjälp av Azure CLI.

> [!NOTE]
> Vissa typer av loggnings buffert skriver till logg filen, vilket kan leda till att det inte finns några händelser i data strömmen. Till exempel kan en program logg post som inträffar när en användare besöker en sida visas i data strömmen före motsvarande HTTP-loggpost för sid förfrågan.
>
> [!NOTE]
> Logg strömning strömmar också information som skrivs till en textfil som lagras i mappen **D\\:\\\\ hem** -LogFiles.
>
>

### <a name="streaming-with-azure-cli"></a>Strömma med Azure CLI
Om du vill strömma loggnings information öppnar du en ny kommando tolk, PowerShell, bash eller terminalsession och anger följande kommando:

    az webapp log tail --name appname --resource-group myResourceGroup

Det här kommandot ansluter till appen med namnet APPNAME och börjar strömma information till fönstret när logg händelser inträffar i appen. All information som skrivs till filer som slutar på. txt,. log eller. htm som lagras i katalogen/LogFiles (d:/Home/logfile) strömmas till den lokala konsolen.

Om du vill filtrera vissa händelser, till exempel fel, använder du parametern **--filter** . Exempel:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Använd parametern **--Path** för att filtrera vissa logg typer, till exempel http. Exempel:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Om du inte har installerat Azure CLI eller inte har konfigurerat det för att använda din Azure-prenumeration, se [hur du använder Azure CLI](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a>Hur: Förstå diagnostikloggar
### <a name="application-diagnostics-logs"></a>Program diagnostikloggar
Programdiagnostik lagrar information i ett särskilt format för .NET-program, beroende på om du lagrar loggar till fil systemet eller blob-lagringen. 

Den grundläggande uppsättningen lagrade data är samma för båda lagrings typerna – datum och tid då händelsen inträffade, process-ID som skapade händelsen, händelse typen (information, varning, fel) och händelse meddelandet. Att använda fil systemet för logg lagring är användbart när du behöver omedelbar åtkomst för att felsöka ett problem, eftersom loggfilerna uppdateras nära omedelbart. Blob Storage används för arkivering av filer eftersom filerna cachelagras och sedan töms på lagrings behållaren enligt ett schema.

**Filsystem**

Varje rad som loggats till fil systemet eller som tagits emot med direkt uppspelning är i följande format:

    {Date}  PID[{process ID}] {event type/level} {message}

En fel händelse skulle till exempel se ut ungefär som i följande exempel:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Om du loggar in på fil systemet får du den mest grundläggande informationen om de tre tillgängliga metoderna, som endast tillhandahåller tid, process-ID, händelse nivå och meddelande.

**Blob Storage**

När du loggar in på Blob Storage lagras data i CSV-format (kommaavgränsade värden). Ytterligare fält loggas för att ge mer detaljerad information om händelsen. Följande egenskaper används för varje rad i CSV-filen:

| Egenskapsnamn | Värde/format |
| --- | --- |
| Date |Datum och tid då händelsen inträffade |
| Nivå |Händelse nivå (till exempel fel, varning, information) |
| ApplicationName |Appens namn |
| Instans-ID |Instans av appen som händelsen inträffade på |
| EventTickCount |Datum och tid då händelsen inträffade, i skal format (större precision) |
| EventId |Händelse-ID för den här händelsen<p><p>Standardvärdet är 0 om inget anges |
| Personligt |Process-ID |
| Bekräfta |Tråd-ID för tråden som skapade händelsen |
| Message |Händelse informations meddelande |

Data som lagras i en BLOB ser ut ungefär som i följande exempel:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> För ASP.NET Core görs loggning med hjälp av providern [Microsoft. Extensions. logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) providern deponerar ytterligare loggfiler i BLOB-behållaren. Mer information finns i [ASP.net Core loggning i Azure](/aspnet/core/fundamentals/logging).
>
>

### <a name="failed-request-traces"></a>Spårning av misslyckade begär Anden
Spårning av misslyckade begär Anden lagras i XML-filer med namnet **fr # #. XML**. För att göra det enklare att visa den loggade informationen finns en XSL-formatmall med namnet **freb. xsl** i samma katalog som XML-filerna. Om du öppnar en av XML-filerna i Internet Explorer använder Internet Explorer XSL-formatmallen för att tillhandahålla en formaterad visning av spårnings informationen, ungefär som i följande exempel:

![misslyckad förfrågan visad i webbläsaren](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> Ett enkelt sätt att visa de formaterade spårningarna för misslyckade begär Anden är att navigera till appens sida i portalen. På den vänstra menyn väljer du **diagnostisera och löser problem**, sedan söker du efter **spårnings loggar för misslyckade begär Anden**och klickar sedan på ikonen för att bläddra och visa den spårning som du vill använda.
>

### <a name="detailed-error-logs"></a>Detaljerade fel loggar
Detaljerade fel loggar är HTML-dokument som innehåller mer detaljerad information om HTTP-fel som har inträffat. Eftersom de bara är HTML-dokument kan de visas i en webbläsare.

### <a name="web-server-logs"></a>Webbserverloggar
Webb server loggarna formateras med [utökat logg fils format för W3C](/windows/desktop/Http/w3c-logging). Den här informationen kan läsas med hjälp av en text redigerare eller analyseras med hjälp av verktyg som [log parser](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Loggarna som genereras av Azure App Service stöder inte fälten **s-ComputerName**, **s-IP**eller **CS-version** .
>
>

## <a name="nextsteps"></a>Nästa steg
* [Övervaka Azure App Service](web-sites-monitor.md)
* [Felsöka Azure App Service i Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analysera app-loggar i HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
