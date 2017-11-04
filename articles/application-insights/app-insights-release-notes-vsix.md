---
title: "Viktig information för Visual Studio-tillägg för utvecklare Analytics"
description: "De senaste uppdateringarna för Visual Studio tools för utvecklare Analytics."
services: application-insights
documentationcenter: 
author: acearun
manager: carmonm
ms.assetid: 2001db30-efc5-417a-a413-93c1b218975f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: mbullwin
ms.openlocfilehash: b1181698deee265e4b6b2df8bb18e2297cdc8599
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="release-notes-for-developer-analytics-tools"></a>Viktig information för Developer Analytics Tools

## <a name="version-718-visual-studio-2015"></a>Version 7.18 (Visual Studio 2015)

* Förbättrade popup-meddelanden.
* ”Inte” filter i informationen visa händelser i Application Insights sökningen.
* Felkorrigeringar

## <a name="version-86-visual-studio-2017-rtw-and-rc4-and-version-717-visual-studio-2015"></a>8.6 (Visual Studio 2017 RTW och RC4) och versionen 7.17 (Visual Studio 2015)

* Anteckningar märka när du publicerar appen från Visual Studio görs nu till dina data i Metrics Explorer i Azure Portal
* Markörer läggs nu till rullningslisterna i koden filerna motsvarar rött och gult CodeLens varningar från Application Insights
* Uppdatera prisinformation i fönstret konfiguration
* Felkorrigeringar

[Du hittar detaljerad information här](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#devanalytics)

## <a name="version-716-visual-studio-2015"></a>Version 7.16 (Visual Studio 2015)

* Felkorrigeringar

## <a name="version-85-visual-studio-2017-rc3-and-version-715-visual-studio-2015"></a>8.5 (Visual Studio 2017 RC3) och versionen 7.15 (Visual Studio 2015)

* CodeLens visas nu felsöknings- och live telemetridata i projekt som skickar data till Application Insights-resurs
* Programmet insikter prisinformation nu visas i fönstret konfiguration
* CodeLens för begäranden och undantag har nu stöd för ASP.NET-projekt som skrivits i Visual Basic
* Application Insights Sök nu visar icke provade händelse antalet händelser som har samlats in
* Felkorrigeringar

## <a name="version-714-visual-studio-2015"></a>Version 7.14 (Visual Studio 2015)

* Sök stöd för tillgänglighet (webbtest) och visa händelser
* Trender stöd för tillgänglighet (webbtest) och visa händelser
* Diagnostiska verktyg och händelsen information etikett för SDK anpassningsbar provtagning
* Felkorrigeringar

## <a name="version-712-visual-studio-2015"></a>Version 7,12 (Visual Studio 2015)

* Publicera nya meddelande format
* Felkorrigeringar

## <a name="version-84-visual-studio-2017-rc2-and-version-711-visual-studio-2015"></a>8.4 (Visual Studio 2017 RC2) och versionen 7.11 (Visual Studio 2015)

* CodeLens visar begäranden för lokala debug-sessioner för projekt med Application Insights SDK
* CodeLens kan leder dig direkt till programmet analyser för att se effekten av användaren
* Infoga JavaScript för att samla in sidvisningar
* Felkorrigeringar

## <a name="version-710-visual-studio-2015"></a>Version 7.10 (Visual Studio 2015)

* Ny design för fönstret Application Insights-konfigurationen
* Felkorrigeringar

## <a name="version-79-visual-studio-2015"></a>Version 7,9 (Visual Studio 2015)

* CodeLens visar undantag som har inträffat under lokala debug-sessioner för projekt med Application Insights SDK
* Felkorrigeringar

## <a name="version-83-visual-studio-2017-rc-and-version-78-visual-studio-2015"></a>8.3 (Visual Studio 2017 RC) och versionen 7,8 (Visual Studio 2015)

* Nya upplevelsen för att lägga till Application Insights i fönstret konfiguration
* Felkorrigeringar

## <a name="version-77-visual-studio-2015"></a>Version 7,7 (Visual Studio 2015)

* Exaktare mappningar från telemetriska händelser till metoder med anpassade ASP.NET-Routning
* Felkorrigeringar

## <a name="version-76-visual-studio-2015"></a>Version 7.6 (Visual Studio 2015)

* Analysera händelser som är inbegripen i en åtgärd på fliken Spåra igen på händelser i Sök-verktyget
* Felkorrigeringar

## <a name="version-75-visual-studio-2015"></a>Version 7.5 (Visual Studio 2015)

* Telemetri tillverkningsinformation för begäranden i diagnostikverktyg
* Fungera objektet skapades från relaterade objekt i Sök-verktyget
* Felkorrigeringar

## <a name="version-74-visual-studio-2015"></a>Version 7.4 (Visual Studio 2015)

* I trender filterrutan nu ändra storlek
* Felkorrigeringar

## <a name="version-73-visual-studio-2015"></a>Version 7.3 (Visual Studio 2015)

* Begäranden i CodeLens
* Fönstret Systemkonfiguration
* HockeyApp SDK uppdateras till v4.2.2
* Felkorrigeringar

## <a name="version-72-visual-studio-2015"></a>Version 7.2 (Visual Studio 2015)

* Felkorrigeringar

## <a name="version-71-visual-studio-2015"></a>Version 7.1 (Visual Studio 2015)

* Telemetri beredskap för indikatorn i Application Insights trender
* Felkorrigeringar

## <a name="version-70"></a>Version 7.0
### <a name="azure-application-insights-trends"></a>Azure Application Insights trender
Azure Application Insights är ett nytt verktyg i Visual Studio som du kan använda för att analysera hur din app fungerar över tid. Du kommer igång på den **Programinsikter** verktygsfältet knappen eller i Application Insights fönstret Välj **utforska telemetri trender**. Eller på den **visa** -menyn klickar du på **andra fönster**, och klicka sedan på **Application Insights trender**. Välj en av fem vanliga frågor för att komma igång. Du kan analysera olika datauppsättningar baserat på telemetri typer, tidsintervall och andra egenskaper. Söker efter avvikelser i dina data, väljer du något av alternativen för avvikelseidentifiering i den **vytypen** listrutan. Filtreringsalternativen längst ned i fönstret gör det lättare att justera efter specifika delmängder av dina telemetri.

![Trender för Application Insights](./media/app-insights-release-notes-vsix/Trends.png)

### <a name="exceptions-in-codelens"></a>Undantag i CodeLens
Undantagstelemetri visas nu i CodeLens. Om du har anslutit till ditt projekt till Application Insights-tjänsten, visas antalet undantag som uppstått i varje metod i produktion under de senaste 24 timmarna. Från CodeLens, kan du hoppa till sökning eller trender för att undersöka undantag i detalj.

![Undantag i CodeLens](./media/app-insights-release-notes-vsix/ExceptionsCodeLens.png)

### <a name="aspnet-core-support"></a>ASP.NET Core-stöd
Application Insights har nu stöd för ASP.NET Core RC2-projekt i Visual Studio. Du kan lägga till Application Insights nya ASP.NET Core RC2-projekt från den **nytt projekt** dialogrutan, som i följande skärmbild. Eller, du kan lägga till den i ett befintligt projekt, högerklicka på projektet i Solution Explorer och klicka sedan på **Lägg till Application Insights Telemetry**.

![ASP.NET Core-stöd](./media/app-insights-release-notes-vsix/NetCoreSupport.png)

ASP.NET 5 RC1 och ASP.NET Core RC2 projekt har också stöd för nya i fönstret diagnosverktyg. Ser du Application Insights händelser, t.ex. begäranden och undantag från din ASP.NET-app när du felsöker lokalt på din dator. Klicka på från varje händelse **Sök** och öka detaljnivån för mer information.

![Stöd för verktyg för Nätverksdiagnostik](./media/app-insights-release-notes-vsix/DiagnosticTools.png)

### <a name="hockeyapp-for-universal-windows-apps"></a>HockeyApp för universella Windows-appar
Förutom distributions- och feedback om beta tillhandahåller HockeyApp symbolicated krasch rapportering för universella Windows-appar. Vi har gjort det ännu enklare att lägga till HockeyApp-SDK: Högerklicka på projektet universell Windows och klicka sedan på **spela App – aktivera krascher Analytics**. Detta installerar SDK, ställer in krascher samlingen och etablerar en HockeyApp resurs i molnet, utan att överföra appen till HockeyApp-tjänsten.

Andra nya funktioner:

* Vi har gjort Application Insights sökinställningar snabbare och mer intuitiv. Nu används tidsintervall och information om filter automatiskt när du markerar dem.
* Även i Application Insights Sök finns nu ett alternativ för att gå till koden direkt från begärandetelemetri.
* Vi har gjort förbättringar av HockeyApp inloggning.
* Telemetri tillverkningsinformation för undantag som visas i diagnostiska verktyg.

## <a name="version-52"></a>Version 5.2
Vi är glada över att presentera HockeyApp scenarier i Visual Studio. Första integrationen har betafördelningen universella Windows-appar och Windows Forms-program från Visual Studio.

Med betafördelningen överför tidiga versioner av dina appar till HockeyApp för distribution till en valda delmängd av kunder eller testare. Betafördelningen kombineras med HockeyApp krasch insamlings- och feedback funktioner, kan du få värdefull information om appen innan du gör en bred version. Du kan använda den här informationen för att åtgärda problem med din app så att du kan undvika eller minimera framtida problem, till exempel låg app klassificering, negativ feedback och så vidare.

Checka ut hur enkelt det är att överföra bygger för betafördelningen i Visual Studio.

### <a name="universal-windows-apps"></a>Universell Windows-appar
Snabbmenyn för en universell Windows-app projektnoden inkluderar nu ett alternativ för att överföra din build till HockeyApp.

![Projektet snabbmenyn för universella Windows-appar](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

Välj objektet och HockeyApp överför öppnas. Du behöver ett HockeyApp-konto för att överföra din version. Om du är nybörjare oroa dig inte. Skapar ett konto är en enkel process.

När du är ansluten, visas formuläret Överför i dialogrutan.

![Överför dialogrutan för universella Windows-appar](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

Välj innehållet som ska överföras (en .appxbundle eller .appx-fil) och välj versionen alternativ i guiden. Alternativt kan du lägga till viktig information på nästa sida. Välj **Slutför** att påbörja överföringen.

När överföringen är klar, visas ett HockeyApp-meddelande med bekräftelse och en länk till appen på HockeyApp-portalen.

![Överför fullständig anmälan](./media/app-insights-release-notes-vsix/UploadComplete.png)

Klart! Du har just har överfört en version för betafördelningen med bara några klickningar.

Du kan hantera dina program på flera olika sätt i HockeyApp-portalen. Detta inkluderar att bjuda in användare, visa kraschrapporter och feedback, ändrar information och så vidare.

![HockeyApp-portalen](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

Finns det [HockeyApp Knowledge Base](http://support.hockeyapp.net/kb/app-management-2) för mer information om hantering av.

### <a name="windows-forms-apps"></a>Windows Forms-appar
Snabbmenyn för en Windows-formulär projektnoden inkluderar nu ett alternativ för att överföra din build till HockeyApp.

![Projektet snabbmenyn för Windows Forms-appar](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

Då öppnas dialogrutan överför HockeyApp som liknar det i en universell Windows-app.

![Överför dialogrutan för Windows Forms-appar](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

Observera ett nytt fält i den här guiden för att ange versionen av appen. Informationen är ifylld från manifestet för universella Windows-appar. Windows Forms-appar har tyvärr inte motsvarande till den här funktionen. Du måste ange dem manuellt.

Resten av flödet liknar universella Windows-appar: Välj build och Versionsalternativ, lägga till viktig information, ladda upp och hantera i HockeyApp-portalen.

Det är så enkelt som. Prova och berätta för oss vad du tycker.

## <a name="version-43"></a>Version 4.3
### <a name="search-telemetry-from-local-debug-sessions"></a>Sök telemetri från lokala debug-sessioner
Med den här versionen kan du nu söka efter Application Insights telemetri genereras i Visual Studio-felsökningssessionen. Du kan använda Sök bara om du har registrerat din app med Application Insights förut. Nu kan behöver din app bara har installerat för att söka efter lokala telemetri Application Insights SDK.

Om du har ett ASP.NET-program med Application Insights SDK, gör du följande steg för att använda sökning.

1. Felsöka programmet.
2. Öppna Application Insights Sök i något av följande sätt:
   * På den **visa** -menyn klickar du på **andra fönster**, och klicka sedan på **Application Insights Sök**.
   * Klicka på den **Programinsikter** verktygsfältsknappen.
   * I Solution Explorer expanderar **ApplicationInsights.config**, och klicka sedan på **Sök debug session telemetri**.
3. Om du inte har registrerat dig med Application Insights öppnas fönstret Sök i felsökningsläge session telemetri.
4. Klicka på den **Sök** att se din lokala telemetri.

![Överför klar](./media/app-insights-release-notes-vsix/LocalSearch.png)

## <a name="version-42"></a>Version 4.2
Vi lagt till funktioner som gör att söka efter data enklare i samband med händelser, möjlighet att hoppa till kod från mer Datahändelser och en problemfri slutanvändarupplevelse att skicka loggningsdata till Application Insights i den här versionen. Det här tillägget uppdateras varje månad. Om du har feedback eller funktionen begäranden kan skicka det till aidevtools@microsoft.com.

### <a name="no-click-logging-experience"></a>Ingen Klicka loggning upplevelse
Om du redan använder NLog, log4net eller System.Diagnostics.Tracing, inte bekymra dig om att flytta alla dina spår till Application Insights. Vi har integrerat Application Insights loggning korten normal configuration-upplevelse i den här versionen.
Om du redan har en av dessa ramverk för loggning som konfigurerats beskrivs i följande avsnitt hur du hämtar den.
**Om du har redan lagt till Application Insights:**

1. Högerklicka på projektnoden och klicka sedan på **Programinsikter**, och klicka sedan på **konfigurera Application Insights**. Kontrollera att alternativet att lägga till rätt kort i konfigurationsfönstret visas.
2. Du kan också när du skapar lösningen Observera popup-fönstret som visas längst upp åt höger på skärmen, klicka på **konfigurera**.

![Loggning meddelande](./media/app-insights-release-notes-vsix/LoggingToast.png)

När du har loggning nätverkskort kör ditt program och se till att visa data i fliken diagnosverktyg så här:

![spår](./media/app-insights-release-notes-vsix/Traces.png)

### <a name="jump-to-or-find-the-code-where-the-telemetry-event-property-is-emitted"></a>Hoppa till eller Sök efter koden där händelseegenskap telemetri har genererats
Med den nya versionen användaren kan klicka på ett värde i detalj och detta söker efter en matchande sträng i den aktuella öppna lösningen. Resultat visas i Visual Studio ”Sök” resultatlistan enligt nedan:

![Matchning](./media/app-insights-release-notes-vsix/FindMatch.png)

### <a name="new-search-window-for-when-you-are-not-signed-in"></a>Nya sökfönstret för när du är inte inloggad
Vi har förbättrat utseende på Application Insights sökfönstret som hjälper dig att söka efter data medan dina appar är i produktion.

![Sökfönstret](./media/app-insights-release-notes-vsix/SearchWindow.png)

### <a name="see-all-telemetry-events-associated-with-the-event"></a>Visa alla telemetri händelser som är associerade med händelsen
Vi har lagt till en ny flik med fördefinierade frågor för alla data som rör händelsen telemetri användaren visar bredvid fliken för händelseinformation. Till exempel en begäran har ett fält med namnet **åtgärds-ID**. Alla händelser som är kopplade till denna förfrågan har samma värde för **åtgärds-ID**. Om ett undantag uppstår medan åtgärden bearbetar begäran, får undantaget samma åtgärds-ID som en begäran att göra det lättare att hitta. Om du tittar på en begäran, klickar du på **All telemetri för den här åtgärden** att öppna en ny flik som visar nya sökresultat.

![Relaterade objekt](./media/app-insights-release-notes-vsix/RelatedItems.png)

### <a name="forward-and-back-history-in-search"></a>Framåt och bakåt historik i sökningen
Nu kan du gå fram och tillbaka mellan sökresultat.

![Gå tillbaka](./media/app-insights-release-notes-vsix/GoBAck.png)

## <a name="version-41"></a>Version 4.1
Den här versionen innehåller ett antal nya funktioner och uppdateringar. Du måste uppdatera 1 är installerat för att installera den här versionen.

### <a name="jump-from-an-exception-to-method-in-source-code"></a>Gå från ett undantag till metoden i källkoden
Nu om du visar undantag från din produktionsapp i Application Insights sökfönstret kan hoppa du till metoden i koden där undantag uppstår. Behöver du bara har lästs in rätt projekt och Application Insights tar hand om resten! (Om du vill veta mer om Application Insights sökfönstret kan se viktig information för Version 4.0 i följande avsnitt.)

Hur fungerar det? Du kan använda program insikter Sök även om en lösning som inte är öppen. Stacken spårningsområde visar ett informationsmeddelande om och många av objekten i stackspårningen är inte tillgänglig.

Om filen finns, kan vissa objekt kanske länkar, men objektet lösning information visas fortfarande.

Om du klickar på hyperlänken ska du gå till platsen för vald metod i koden. Det kan finnas skillnader i versionsnummer, men funktionen för att hoppa till rätt version av kod, kommer i senare versioner.

![Klicka på Undantagsinformation](./media/app-insights-release-notes-vsix/jumptocode.png)

### <a name="new-entry-points-to-the-search-experience-in-solution-explorer"></a>Nya startpunkter för sökningen upplevelse i Solution Explorer
Du kan nu använda Sök via Solution Explorer.

![Sök i Solution Explorer](./media/app-insights-release-notes-vsix/searchentry.png)

### <a name="displays-a-notification-when-publish-is-completed"></a>Visar ett meddelande när publicerar har slutförts
En dialogruta visas när projektet har publicerats online, så att du kan visa dina Application Insights-data i produktion.

![Publicera fullständig anmälan](./media/app-insights-release-notes-vsix/publishtoast.png)

## <a name="version-40"></a>Version 4.0
### <a name="search-application-insights-data-from-within-visual-studio"></a>Sök Application Insights-data från Visual Studio
Som sökfunktionen i Application Insights-portalen, nu i Visual Studio kan du filtrera och söka i händelsetyper, värden och text och inspektera enskilda händelser.

![Sökfönstret](./media/app-insights-release-notes-vsix/search.png)

### <a name="see-data-coming-from-your-local-computer-in-diagnostic-tools"></a>Visa data som kommer från den lokala datorn i diagnostikverktyg
Du kan visa din telemetri, utöver andra felsökning data på sidan diagnostikverktyg för Visual Studio. Endast ASP.NET 4.5 stöds.

![Diagnostiska verktyg sida](./media/app-insights-release-notes-vsix/diagtools.png)

### <a name="add-the-sdk-to-your-project-without-signing-in-to-azure"></a>Lägg till SDK i projektet utan att logga in till Azure
Du behöver inte längre logga in på Azure och Lägg till Application Insights-paket i projektet, antingen via den **nytt projekt** dialogrutan eller på snabbmenyn för projektet. Om du loggar in, kommer SDK installeras och konfigureras för att skicka telemetri till portalen som innan. Om du inte logga in SDK kommer att läggas till ditt projekt och det genererar telemetri för diagnostiska hubben. Du kan konfigurera den senare om du vill.

![Dialogrutan Nytt projekt](./media/app-insights-release-notes-vsix/newproject.png)

### <a name="device-support"></a>Stöd för enheter
Vid *Connect();* 2015, vi [meddelats](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) att våra mobila developer upplevelse av enheter är HockeyApp. HockeyApp hjälper dig att distribuera beta versioner till din Testare, samla in och analysera alla krascher från din app och samla in feedback direkt från dina kunder.
HockeyApp stöder din app på vilken plattform som du väljer att skapa den, om den finnas iOS, Android eller Windows eller en lösning för flera plattformar som Xamarin, Cordova eller Unity.

I framtida versioner av Application Insights-tillägget Lär mer integrerad upplevelse mellan HockeyApp och Visual Studio. Nu kan du börja med HockeyApp genom att lägga till NuGet-referens. Finns det [dokumentationen](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) för mer information.
