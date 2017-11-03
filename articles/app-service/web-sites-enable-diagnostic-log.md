---
title: "Aktivera diagnostikloggning för web apps i Azure App Service"
description: "Lär dig hur du aktiverar du diagnostikloggning och lägga till instrumentation i ditt program, samt hur du kommer åt information som loggas av Azure."
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
ms.openlocfilehash: 42644bd97443e827b523503989b0b25d3d48028c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Aktivera diagnostikloggning för web apps i Azure App Service
## <a name="overview"></a>Översikt
Azure tillhandahåller inbyggda diagnostik för att underlätta felsökning en [Apptjänst webbapp](http://go.microsoft.com/fwlink/?LinkId=529714). I den här artikeln lär du dig hur du aktiverar du diagnostikloggning och lägga till instrumentation i ditt program, samt hur du kommer åt information som loggas av Azure.

Den här artikeln används den [Azure Portal](https://portal.azure.com), Azure PowerShell och Azure-kommandoradsgränssnittet (Azure CLI) för att fungera med diagnostikloggar. Information om hur du arbetar med diagnostikloggar med Visual Studio finns i [felsöka Azure i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Web serverdiagnostik- och programdiagnostik
App Service web apps ange diagnostikfunktion för att logga information från både webbservern och webbprogrammet. Dessa logiskt är indelade i **web serverdiagnostik** och **programdiagnostik**.

### <a name="web-server-diagnostics"></a>Web serverdiagnostik
Du kan aktivera eller inaktivera följande typer av loggar:

* **Detaljerad felloggning** -information om felet för HTTP-statuskoder som indikerar att en (statuskod 400 eller högre). Detta kan innehålla information som kan hjälpa dig att avgöra varför servern returnerade felkoden.
* **Kunde inte begäran spårning** -detaljerad information om misslyckade förfrågningar, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och tidsåtgång i varje komponent. Detta kan vara användbart om du försöker öka prestanda för webbplatsen eller isolera vad som orsakar ett specifikt HTTP-fel som ska returneras.
* **Web Server-loggning** -Information om HTTP-transaktioner med hjälp av den [W3C utökat loggfilsformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Detta är användbart när du fastställer övergripande plats mätvärden, till exempel antalet förfrågningar som hanteras eller hur många förfrågningar som kommer från en specifik IP-adress.

### <a name="application-diagnostics"></a>Programdiagnostik
Programdiagnostik kan du samla in information som produceras av ett webbprogram. ASP.NET-program kan använda den [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) klassen för att logga information till diagnostik programloggen. Exempel:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Du kan hämta dessa loggar för felsökning vid körning. Mer information finns i [felsöka Azure web apps i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

App Service web apps även logga information om distribution när du publicerar innehåll till en webbapp. Detta sker automatiskt och det finns inga konfigurationsinställningar för loggning för distribution. Loggning för distribution kan du avgöra varför en distribution misslyckades. Till exempel om du använder ett anpassat distributionsskriptet, kan du använda deployment loggning för att avgöra varför skriptet inte.

## <a name="enablediag"></a>Så här aktiverar du diagnostik
Aktivera diagnostik i den [Azure Portal](https://portal.azure.com), gå till bladet för din webbapp och klickar på **Inställningar > diagnostik loggar**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![En del loggar](./media/web-sites-enable-diagnostic-log/logspart.png)

När du aktiverar **programdiagnostik** du också välja den **nivå**. Den här inställningen kan du filtrera den information som har hämtats till **informationsmeddelande**, **varning** eller **fel** information. Att **utförlig** loggar alla information som genereras av programmet.

> [!NOTE]
> Till skillnad från ändrar web.config-filen, återanvändning aktiverar programdiagnostik eller ändrar diagnostiska loggningsnivåer tillämpningsdomän som programmet körs inom.
>
>

I den [klassiska portalen](https://manage.windowsazure.com) webbapp **konfigurera** fliken kan du välja **lagring** eller **filsystem** för **web server-loggning**. Att välja **lagring** kan du välja ett lagringskonto och en blobbbehållare som skrivs till loggarna. Alla loggar för **plats diagnostik** skrivs till endast filsystemet.

Den [klassiska portalen](https://manage.windowsazure.com) webbapp **konfigurera** fliken finns också ytterligare inställningar för application diagnostics:

* **Filsystem** -lagrar diagnostik programinformationen till filsystemet web app. Dessa filer kan nås av FTP eller hämtas som en Zip-arkiv med hjälp av Azure PowerShell eller Azure-kommandoradsgränssnittet (Azure CLI).
* **Table storage** -lagrar diagnostik programinformationen i det angivna namnet för Azure Storage-konto och tabell.
* **BLOB storage** -programmet diagnostikinformation lagras i den angivna Azure Storage-konto och blob-behållaren.
* **Kvarhållningsperioden** -som standard loggar tas inte bort automatiskt från **blob storage**. Välj **ange kvarhållning** och ange antalet dagar att behålla loggarna om du vill ta bort loggar automatiskt.

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
> Diagnostiken kan även aktiveras från Azure PowerShell med hjälp av den **Set AzureWebsite** cmdlet. Om du inte har installerat Azure PowerShell eller inte har konfigurerat den att använda Azure-prenumeration, se [hur du använder Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

## <a name="download"></a>Så här: hämta loggar
Diagnostisk information som lagras i filsystemet app kan nås med hjälp av FTP. Det kan också hämtas som en Zip-arkiv med hjälp av Azure PowerShell eller Azure-kommandoradsgränssnittet.

Katalogstrukturen som lagrats i loggarna är som följer:

* **Programloggarna** -/LogFiles/program /. Den här mappen innehåller en eller flera textfiler som innehåller information som genereras av programloggning.
* **Det gick inte begäranden** -/ loggfiler/W3SVC ### /. Den här mappen innehåller en XSL-fil och en eller flera XML-filer. Se till att du hämtar den XSL-fil i samma katalog som XML-filerna eftersom XSL-filen innehåller funktioner för att formatera och filtrera innehållet i XML-filen när den visas i Internet Explorer.
* **Detaljerade felloggar** -/LogFiles/DetailedErrors /. Den här mappen innehåller en eller flera .htm-filer som innehåller omfattande information för HTTP-fel som har inträffat.
* **Web Server-loggar** -/LogFiles/http/RawLogs. Den här mappen innehåller en eller flera textfiler formaterats med den [W3C utökat loggfilsformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Distributionsloggar** -loggfilerna/Git. Den här mappen innehåller loggar som genereras av de interna distributionsprocesser som används av Azure-webbappar samt loggar för Git-distributioner.

### <a name="ftp"></a>FTP
För att komma åt diagnostisk information som med FTP, finns det **instrumentpanelen** av ditt webbprogram i den [klassiska portalen](https://manage.windowsazure.com). I den **snabböversikten** Använd den **FTP diagnostikloggar** länken för att komma åt filerna med hjälp av FTP. Den **distribution/FTP-användare** post visar det användarnamn som ska användas för åtkomst till FTP-platsen.

> [!NOTE]
> Om den **distribution/FTP-användare** transaktionen inte har angetts eller du har glömt lösenordet för den här användaren kan du skapa en ny användare och lösenord med hjälp av den **återställa distributionsbehörigheterna** länken i den **snabböversikten** avsnitt i den **instrumentpanelen**.
>
>

### <a name="download-with-azure-powershell"></a>Hämta med Azure PowerShell
Starta en ny instans av Azure PowerShell för att hämta filerna, och använder du följande kommando:

    Save-AzureWebSiteLog -Name webappname

Detta sparar loggar för webbprogram som anges av den **-namnet** parameter till en fil med namnet **logs.zip** i den aktuella katalogen.

> [!NOTE]
> Om du inte har installerat Azure PowerShell eller inte har konfigurerat den att använda Azure-prenumeration, se [hur du använder Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="download-with-azure-command-line-interface"></a>Hämta med Azure-kommandoradsgränssnittet
För att hämta filerna med hjälp av Azure-kommandoradsgränssnittet, öppna en ny kommandotolk, PowerShell, Bash eller terminalsession och ange följande kommando:

    azure site log download webappname

Detta sparar loggar för webbprogrammet med namnet 'webappname' till en fil med namnet **diagnostics.zip** i den aktuella katalogen.

> [!NOTE]
> Om du inte har installerat Azure-kommandoradsgränssnittet (Azure CLI) eller inte har konfigurerat den att använda Azure-prenumeration, se [hur du använder Azure CLI](../cli-install-nodejs.md).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Så här: visa loggar i Application Insights
Programinsikter för Visual Studio innehåller verktyg för att filtrera och söka i loggar och för korrelation mellan loggarna med begäranden och andra händelser.

1. Lägg till Application Insights SDK i ditt projekt i Visual Studio.
   * Högerklicka på projektet i Solution Explorer och välja Lägg till Application Insights. Du kommer att få vägledning genom steg som omfattar att skapa en Application Insights-resurs. [Läs mer](../application-insights/app-insights-asp-net.md)
2. Lägga till spårningsavlyssningen paket i projektet.
   * Högerklicka på projektet och välj Hantera NuGet-paket. Välj `Microsoft.ApplicationInsights.TraceListener` [Läs mer](../application-insights/app-insights-asp-net-trace-logs.md)
3. Överför ditt projekt och kör det att generera loggdata.
4. I den [Azure Portal](https://portal.azure.com/), bläddra till din nya Application Insights-resurs och öppna **Sök**. Du ser ditt loggdata tillsammans med begäran, användning och andra telemetri. Vissa telemetri kan ta några minuter innan den tas emot: Klicka på Uppdatera. [Läs mer](../application-insights/app-insights-diagnostic-search.md)

[Mer information om prestanda spårning med Application Insights](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a>Så här: strömma loggar
När du utvecklar ett program, är det ofta praktiskt att visa loggningsinformation i nära realtid. Detta kan åstadkommas genom att logga information i din utvecklingsmiljö med hjälp av Azure PowerShell eller Azure-kommandoradsgränssnittet för strömning.

> [!NOTE]
> Vissa typer av loggning buffert skriva till loggfilen, vilket kan leda till oordnade händelser i dataströmmen. Exempelvis kan en loggpost för program som uppstår när en användare besöker en sida visas i dataströmmen innan motsvarande HTTP-loggpost för begäran.
>
> [!NOTE]
> Loggen strömning direktuppspelas också information som skrivs till en textfil som lagras i den **D:\\hem\\loggfiler\\**  mapp.
>
>

### <a name="streaming-with-azure-powershell"></a>Strömning med Azure PowerShell
Att strömma loggningsinformation och starta om en ny instans av Azure PowerShell använder du följande kommando:

    Get-AzureWebSiteLog -Name webappname -Tail

Ansluts till det webbprogram som anges av den **-namnet** parameter och börja strömning information till PowerShell-fönstret när händelser inträffar kontinuerligt i webbprogrammet. All information som skrivs till filer som slutar på .txt, .log eller .htm som lagras i katalogen /LogFiles (d:/home/loggfilerna) kommer att strömmas till den lokala konsolen.

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

    azure site log tail webappname

Detta ska ansluta till webbprogrammet med namnet 'webappname' och börja strömning information till fönstret när händelser inträffar kontinuerligt i webbprogrammet. All information som skrivs till filer som slutar på .txt, .log eller .htm som lagras i katalogen /LogFiles (d:/home/loggfilerna) kommer att strömmas till den lokala konsolen.

För att filtrera specifika händelser, till exempel fel, använder den **--Filter** parameter. Exempel:

    azure site log tail webappname --filter Error

För att filtrera viss loggning typer, till exempel HTTP, använder den **--sökväg** parameter. Exempel:

    azure site log tail webappname --path http

> [!NOTE]
> Om du inte har installerat Azure-kommandoradsgränssnittet eller inte har konfigurerat den att använda Azure-prenumeration, se [så att använda Azure-kommandoradsgränssnittet](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a>Så här: Förstå diagnostik loggar
### <a name="application-diagnostics-logs"></a>Programloggarna för diagnostik
Programdiagnostik lagrar information i ett specifikt format för .NET-program, beroende på om du lagrar loggar till filsystemet, tabellagring eller blob-lagring. En grundläggande uppsättning data som lagras är samma i alla tre lagringstyper - datum och tid som händelsen inträffade, process-ID som producerade händelsen, vilken typ av händelse (information, varning, fel) och händelsemeddelandet.

**Filsystem**

Varje rad inloggade filsystemet eller tas emot med streaming blir i följande format:

    {Date}  PID[{process id}] {event type/level} {message}

Till exempel visas en felhändelse liknar följande:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Loggning till filsystemet ger den mest grundläggande informationen av tre tillgängliga metoder ger endast tid, process-id, Händelsenivå och meddelandet.

**Table Storage**

När de loggar till tabellagring används ytterligare egenskaper som för att underlätta söka data som lagras i tabellen samt mer detaljerad information om händelsen. Följande egenskaper (kolumner) används för varje entitet (rad) som lagras i tabellen.

| Egenskapsnamn | Value-format |
| --- | --- |
| PartitionKey |Datum/tid för händelse i yyyyMMddHH format |
| RowKey |Ett GUID-värde som unikt identifierar den här entiteten |
| tidsstämpel |Datum och tid då händelsen inträffade |
| EventTickCount |Datum och tid då händelsen inträffade i Tick-format (större precision) |
| ApplicationName |Webbprogramnamnet |
| Nivå |Händelsenivå (t.ex. fel, varning, information) |
| Händelse-ID |Händelse-ID för den här händelsen<p><p>Standardvärdet är 0 om inget anges |
| InstanceId |Instans av webbappen som den även inträffade |
| Process-ID |Process-ID |
| tid |Tråd-ID för tråden som skapades av händelsen |
| Meddelande |Detaljerat meddelande |

**Blob Storage**

När loggning för att blob storage, lagras data i fil med kommaavgränsade värden (CSV)-format. Liknar tabellagring, ytterligare fält som loggas med mer detaljerad information om händelsen. Följande egenskaper används för varje rad i CSV-filen:

| Egenskapsnamn | Value-format |
| --- | --- |
| Date |Datum och tid då händelsen inträffade |
| Nivå |Händelsenivå (t.ex. fel, varning, information) |
| ApplicationName |Webbprogramnamnet |
| InstanceId |Instans av webbappen som händelsen inträffade |
| EventTickCount |Datum och tid då händelsen inträffade i Tick-format (större precision) |
| Händelse-ID |Händelse-ID för den här händelsen<p><p>Standardvärdet är 0 om inget anges |
| Process-ID |Process-ID |
| tid |Tråd-ID för tråden som skapades av händelsen |
| Meddelande |Detaljerat meddelande |

Data som lagras i en blob skulle se ut ungefär så här:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Den första raden i loggen innehåller kolumnrubrikerna som representeras i det här exemplet.
>
>

### <a name="failed-request-traces"></a>Det gick inte begäranden
Misslyckade begäranden lagras i XML-filer med namnet **fr ### .xml**. För att göra det enklare att visa information om loggade XSL-formatmallar med namnet **freb.xsl** finns i samma katalog som XML-filerna. Öppna en XML-filerna i Internet Explorer använder XSL-formatmallar för att tillhandahålla en formaterad visningen av spårningsinformationen. Detta liknar följande:

![misslyckade begäranden som visas i webbläsaren](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Detaljerade felloggar
Detaljerade felloggar är HTML-dokument som innehåller mer detaljerad information om HTTP-fel som har inträffat. Eftersom de bara HTML-dokument, kan de visas i en webbläsare.

### <a name="web-server-logs"></a>Webbserverloggarna
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
> Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i Apptjänst. Inget kreditkort krävs, och du gör inga åtaganden.
>
>

## <a name="whats-changed"></a>Nyheter
* En guide till övergången från Webbplatser till App Service finns i: [Azure App Service och dess påverkan på befintliga Azure-tjänster](http://go.microsoft.com/fwlink/?LinkId=529714)
* En guide till ändring av den gamla portalen till den nya portalen finns: [referens för navigering Azure-portalen](http://go.microsoft.com/fwlink/?LinkId=529715)
