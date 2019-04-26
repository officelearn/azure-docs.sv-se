---
title: Viktig information för Visual Studio-tillägg för Developer Analytics
description: De senaste uppdateringarna för Visual Studio tools för Developer Analytics.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2001db30-efc5-417a-a413-93c1b218975f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/20/2017
ms.reviewer: aruna
ms.author: mbullwin
ms.openlocfilehash: 0befc5e00cf31960a0ecda3f739e65477c740edd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60374152"
---
# <a name="release-notes-for-developer-analytics-tools"></a>Viktig information för Developer Analytics Tools

## <a name="version-718-visual-studio-2015"></a>Version 7.18 (Visual Studio 2015)

* Omarbetad popup-meddelanden.
* ”Not” filter i detaljerat visa för händelser i Application Insights-sökning.
* Felkorrigeringar

## <a name="version-86-visual-studio-2017-rtw-and-rc4-and-version-717-visual-studio-2015"></a>Version 8.6 (Visual Studio 2017 RTW och RC4) och Version 7.17 (Visual Studio 2015)

* Anteckningar märka när du publicerar en app från Visual Studio görs nu till dina data i Måttutforskaren i Azure Portal
* Markörer läggs nu till rullningslister i kodfiler, motsvarar rött och gult CodeLens-varningar från Application Insights
* Uppdaterad prissättning information i fönstret konfiguration
* Felkorrigeringar

[Se den detaljerade informationen här](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#devanalytics)

## <a name="version-716-visual-studio-2015"></a>Version 7.16 (Visual Studio 2015)

* Felkorrigeringar

## <a name="version-85-visual-studio-2017-rc3-and-version-715-visual-studio-2015"></a>Version 8.5 (Visual Studio 2017 RC3) och Version 7.15 (Visual Studio 2015)

* CodeLens visar nu både felsöknings- och live telemetridata i projekt som skickar data till en Application Insights-resurs
* Application Insights prisinformation nu visas i fönstret konfiguration
* CodeLens för begäranden och undantag har nu stöd för ASP.NET-projekt som är skrivna i Visual Basic
* Application Insights Search visar nu antalet icke provade händelser för händelser som har varit samplas
* Felkorrigeringar

## <a name="version-714-visual-studio-2015"></a>Version 7.14 (Visual Studio 2015)

* Sök efter support för tillgänglighet (webbtest) och sidvisningshändelser
* Trender stöd för tillgänglighet (webbtest) och sidvisningshändelser
* Diagnostiska verktyg och händelsen information etikett för SDK Adaptiv Sampling
* Felkorrigeringar

## <a name="version-712-visual-studio-2015"></a>Version 7.12 (Visual Studio 2015)

* Publicera nya meddelandeformat
* Felkorrigeringar

## <a name="version-84-visual-studio-2017-rc2-and-version-711-visual-studio-2015"></a>Version 8.4 (Visual Studio 2017 RC2) och Version 7.11 (Visual Studio 2015)

* CodeLens visar begäranden för lokal felsökning sessioner för projekt med Application Insights SDK
* CodeLens kan leder dig direkt till Application Analytics för att se användarna påverkas
* Infoga JavaScript för att samla in sidvisningar
* Felkorrigeringar

## <a name="version-710-visual-studio-2015"></a>Version 7.10 (Visual Studio 2015)

* Ny design för konfiguration av Application Insights-fönstret
* Felkorrigeringar

## <a name="version-79-visual-studio-2015"></a>Version 7.9 (Visual Studio 2015)

* CodeLens visar undantag som har inträffat under lokal felsökning sessioner för projekt med Application Insights SDK
* Felkorrigeringar

## <a name="version-83-visual-studio-2017-rc-and-version-78-visual-studio-2015"></a>Version 8.3 (Visual Studio 2017 RC) och Version 7.8 (Visual Studio 2015)

* Ny upplevelse för att lägga till Application Insights i fönstret konfiguration
* Felkorrigeringar

## <a name="version-77-visual-studio-2015"></a>Version 7.7 (Visual Studio 2015)

* Mer exakta mappningar från telemetrihändelser till metoder med hjälp av anpassade ASP.NET-Routning
* Felkorrigeringar

## <a name="version-76-visual-studio-2015"></a>Version 7.6 (Visual Studio 2015)

* Analysera händelser som ingår i en åtgärd från fliken Spåra åtgärd på händelser i sökverktyget
* Felkorrigeringar

## <a name="version-75-visual-studio-2015"></a>Version 7.5 (Visual Studio 2015)

* Produktion telemetriinformation för begäranden i diagnostikverktyg
* Arbeta objektet skapades från relaterade objekt i sökverktyget
* Felkorrigeringar

## <a name="version-74-visual-studio-2015"></a>Version 7.4 (Visual Studio 2015)

* Filterfönstret i trender går nu att ändra storlek
* Felkorrigeringar

## <a name="version-73-visual-studio-2015"></a>Version 7.3 (Visual Studio 2015)

* Begäranden i CodeLens
* Konfigurationsfönstret
* HockeyApp SDK uppdateras till v4.2.2
* Felkorrigeringar

## <a name="version-72-visual-studio-2015"></a>Version 7.2 (Visual Studio 2015)

* Felkorrigeringar

## <a name="version-71-visual-studio-2015"></a>Version 7.1 (Visual Studio 2015)

* Telemetri beredskap indikatorn i Application Insights Trends
* Felkorrigeringar

## <a name="version-70"></a>Version 7.0
### <a name="azure-application-insights-trends"></a>Azure Application Insights Trends
Azure Application Insights är ett nytt verktyg i Visual Studio som du kan använda för att analysera hur din app fungerar med tiden. Du kommer igång på den **Programinsikter** verktygsfältet knappen eller i Application Insights-sökfönstret väljer **utforska Telemetritrender**. Eller på den **visa** -menyn klickar du på **andra Windows**, och klicka sedan på **Application Insights Trends**. Välj en av fem vanliga frågor för att komma igång. Du kan analysera olika datauppsättningar baserat på telemetrityper, tidsintervall och andra egenskaper. För att identifiera avvikelser i dina data, väljer du något av alternativen avvikelser i den **vytypen** listrutan. Filtreringsalternativen längst ned i fönstret gör det lättare att justera efter specifika delmängder av dina telemetri.

![Application Insights Trends](./media/release-notes-vsix/Trends.png)

### <a name="exceptions-in-codelens"></a>Undantag i CodeLens
Undantagstelemetri visas nu i CodeLens. Om du har anslutit ditt projekt till Application Insights-tjänsten, visas antalet undantag som har inträffat under varje metod i produktion under de senaste 24 timmarna. Från CodeLens, kan du gå till Sök- eller trender för att undersöka undantagen i detalj.

![Undantag i CodeLens](./media/release-notes-vsix/ExceptionsCodeLens.png)

### <a name="aspnet-core-support"></a>Stöd för ASP.NET Core
Application Insights har nu stöd för ASP.NET Core RC2-projekt i Visual Studio. Du kan lägga till Application Insights till den nya ASP.NET Core RC2 projekt från den **nytt projekt** dialogrutan, som i följande skärmbild. Eller, du kan lägga till den i ett befintligt projekt, högerklicka på projektet i Solution Explorer och klicka sedan på **Lägg till Application Insights Telemetry**.

![Stöd för ASP.NET Core](./media/release-notes-vsix/NetCoreSupport.png)

ASP.NET 5 RC1 och ASP.NET Core RC2 projekt måste du också ha stöd för nya i fönstret diagnostikverktyg. Du ser Application Insights-händelser som begäranden och undantag från din ASP.NET-app när du felsöker lokalt på din dator. Varje händelse klickar du på **Search** och öka detaljnivån för mer information.

![Stöd för diagnostikverktyg](./media/release-notes-vsix/DiagnosticTools.png)

### <a name="hockeyapp-for-universal-windows-apps"></a>HockeyApp för universella Windows-appar
Förutom beta-distribution och Användarfeedback tillhandahåller HockeyApp symbolicated kraschrapportering för din universella Windows-appar. Vi har gjort det ännu enklare att lägga till HockeyApp SDK: Högerklicka på din universella Windows-projektet och klicka sedan på **Hockey App - aktivera krascher Analytics**. Detta installerar SDK, ställer in krascher samling och etablerar en HockeyApp-resurs i molnet, utan att behöva ladda upp din app till HockeyApp-tjänsten.

Andra nya funktioner:

* Vi har gjort Application Insights sökupplevelsen snabbare och mer intuitiv användning. Nu, tidsintervall och information om filter tillämpas automatiskt när du markerar dem.
* I Application Insights Search nu är det också ett alternativ för att går du vidare till koden direkt från begärandetelemetri som.
* Vi har gjort förbättringar för metoderna att logga in HockeyApp.
* Produktion telemetriinformation för undantag visas i diagnostiska verktyg.

## <a name="version-52"></a>Version 5.2
Vi har glädjen att meddela introduktionen av HockeyApp scenarier i Visual Studio. Första integrationen är i betafördelningen universella Windows-appar och Windows Forms-appar från Visual Studio.

Med betafördelningen överföra tidiga versioner av dina appar till HockeyApp för distribution till en valda delmängd av kunder eller testare. Betadistribuera, i kombination med HockeyApp krascher insamling och användaren feedback funktioner, kan du få värdefull information om din app innan du gör en bred version. Du kan använda denna information för att lösa problem med din app så att du kan undvika eller minimera framtida problem, till exempel låg app klassificeringar, negativ feedback och så vidare.

Kolla in hur enkelt det är att överföra skapar för betafördelningen från Visual Studio.

### <a name="universal-windows-apps"></a>Universell Windows-appar
Snabbmenyn för en Universal Windows app-projektnoden innehåller nu ett alternativ för att ladda upp din version till HockeyApp.

![Snabbmenyn för projektet för universella Windows-appar](./media/release-notes-vsix/UniversalContextMenu.png)

Välj objektet och HockeyApp dialogruta för filöverföring öppnas. Du behöver ett HockeyApp-konto för att ladda upp din version. Om du är nybörjare oroa dig inte. Det är en enkel process för att skapa ett konto.

När du är ansluten, visas formuläret ladda upp i dialogrutan.

![Ladda upp dialogrutan för universella Windows-appar](./media/release-notes-vsix/UniversalUploadDialog.png)

Välj innehållet du överför (en .appxbundle eller .appx-fil) och välj sedan alternativ för versionen i guiden. Du kan också lägga till viktig information på nästa sida. Välj **Slutför** att påbörja uppladdningen.

När överföringen är klar, visas ett meddelande för HockeyApp med bekräftelse och en länk till appen i HockeyApp-portalen.

![Ladda upp meddelande om slutförd](./media/release-notes-vsix/UploadComplete.png)

Klart! Du har just har överfört en version för betafördelningen med bara några klick.

Du kan hantera ditt program i flera olika sätt i HockeyApp-portalen. Detta inkluderar att bjuda in användare som visar kraschrapporter och feedback, ändra information och så vidare.

![HockeyApp-portalen](./media/release-notes-vsix/HockeyAppPortal.png)

Se den [HockeyApp Knowledge Base](https://support.hockeyapp.net/kb/app-management-2) för mer information om hantering av.

### <a name="windows-forms-apps"></a>Windows Forms-appar
Snabbmenyn för en Windows-formulär projektnoden innehåller nu ett alternativ för att ladda upp din version till HockeyApp.

![Snabbmenyn för projektet för Windows Forms-appar](./media/release-notes-vsix/WinFormContextMenu.png)

Då öppnas dialogrutan överför HockeyApp som liknar det i en universell Windows-app.

![Ladda upp dialogrutan för Windows Forms-appar](./media/release-notes-vsix/WinFormsUploadDialog.png)

Observera ett nytt fält i den här guiden för att ange versionen av appen. För universella Windows-appar fylls informationen från manifestet. Windows Forms-appar har tyvärr inte motsvarigheten till den här funktionen. Du måste ange dem manuellt.

Resten av flödet påminner om universella Windows-appar: Välj alternativ för Azure Pipelines, lägga till versionen anteckningar, ladda upp och hantera i HockeyApp-portalen.

Det är lika enkelt som. Prova och berätta för oss vad du tycker.

## <a name="version-43"></a>Version 4.3
### <a name="search-telemetry-from-local-debug-sessions"></a>Sök telemetri från lokal felsökning sessioner
Den här versionen kan söka du nu efter Application Insights-telemetri som genereras i Visual Studio-felsökningssessionen. Du kan använda Sök endast om du har registrerat din app med Application Insights innan. Nu kan behöver din app bara ha installerat för att söka efter lokala telemetri Application Insights SDK.

Om du har ett ASP.NET-program med Application Insights SDK gör du följande steg för att söka på.

1. Felsöka programmet.
2. Öppna Application Insights sökning i något av följande sätt:
   * På den **visa** -menyn klickar du på **andra Windows**, och klicka sedan på **Application Insights Search**.
   * Klicka på den **Application Insights** knapp i verktygsfältet.
   * I Solution Explorer expanderar **ApplicationInsights.config**, och klicka sedan på **Search debug session telemetry**.
3. Om du inte har registrerat dig med Application Insights, öppnas fönstret Sök i felsökningsläge session telemetri.
4. Klicka på den **Search** så visas din lokala telemetri.

![Uppladdningen är klar](./media/release-notes-vsix/LocalSearch.png)

## <a name="version-42"></a>Version 4.2
I den här versionen har vi lagt till funktioner som gör att söka efter data enklare i samband med händelser, med möjlighet att hoppa till kod från Datahändelser för mer och en smidig upplevelse att skicka in data till Application Insights. Det här tillägget uppdateras varje månad. Om du har feedback eller funktionsönskemål kan skicka den till aidevtools@microsoft.com.

### <a name="no-click-logging-experience"></a>Nej klick loggning upplevelse
Om du redan använder NLog, log4net eller System.Diagnostics.Tracing, har du inte bekymra dig om hur du flyttar alla dina spårningar till Application Insights. I den här versionen har vi integrerat loggning för Application Insights-kort med den normala konfigurationsupplevelse.
Om du redan har en av dessa loggningsramverk som konfigurerats beskrivs i följande avsnitt hur du hämtar den.
**Om du har redan lagt till Application Insights:**

1. Högerklicka på projektnoden och klicka sedan på **Programinsikter**, och klicka sedan på **konfigurera Application Insights**. Kontrollera att du ser att lägga till rätt kort i konfigurationsfönstret.
2. Du kan också när du skapar lösningen Observera popup-fönstret som visas längst upp till höger på skärmen, klickar du på **konfigurera**.

![Loggningsaviseringar](./media/release-notes-vsix/LoggingToast.png)

När du har loggning nätverkskort som är installerade, kör ditt program och se till att visa data i fliken diagnostikverktyg så här:

![Spårningar](./media/release-notes-vsix/Traces.png)

### <a name="jump-to-or-find-the-code-where-the-telemetry-event-property-is-emitted"></a>Gå till eller hitta koden där egenskapen telemetri händelsen har genererats
Med den nya versionen användaren kan klicka på valfritt värde i detalj och det görs en sökning efter en matchande sträng i den aktuella öppna lösningen. Resultaten visas i Visual Studio ”hitta” resultatlistan enligt nedan:

![Hitta matchning](./media/release-notes-vsix/FindMatch.png)

### <a name="new-search-window-for-when-you-are-not-signed-in"></a>Nytt sökfönster för när du har inte loggat in
Vi har förbättrat utseende på Application Insights-sökfönstret för att söka i dina data medan appen är i produktion.

![Sökfönster](./media/release-notes-vsix/SearchWindow.png)

### <a name="see-all-telemetry-events-associated-with-the-event"></a>Se alla telemetrihändelser som associerats till händelsen
Vi har lagt till en ny flik med fördefinierade frågor för alla data som rör händelsen telemetri användaren visar bredvid fliken för information om händelsen. En begäran har exempelvis ett fält med namnet **åtgärds-ID**. Varje händelse som är kopplad till den här begäran har samma värde för **åtgärds-ID**. Om ett undantag uppstår medan åtgärden bearbetar begäran, får undantaget samma åtgärds-ID som förfrågan om att göra det enklare att hitta. Om du tittar på en begäran, klickar du på **All telemetri för den här åtgärden** att öppna en ny flik som visar nya sökresultat.

![Relaterade objekt](./media/release-notes-vsix/RelatedItems.png)

### <a name="forward-and-back-history-in-search"></a>Framåt och bakåt historiken i sökning
Nu går du fram och tillbaka mellan sökresultat.

![Gå tillbaka](./media/release-notes-vsix/GoBAck.png)

## <a name="version-41"></a>Version 4.1
Den här versionen innehåller ett antal nya funktioner och uppdateringar. Du måste ha uppdatering 1 är installerat för att installera den här versionen.

### <a name="jump-from-an-exception-to-method-in-source-code"></a>Gå från ett undantag till metoden i källkoden
Nu, om du visar undantag från din produktionsapp i sökfönstret för Application Insights kan du hoppa till metoden i din kod var undantaget uppstår. Du behöver bara har lästs in rätt projekt och Application Insights tar hand om resten! (Om du vill veta mer om Application Insights-sökfönstret kan se viktig information för Version 4.0 i följande avsnitt.)

Hur fungerar det? Du kan använda program insikter Sök även när en lösning som inte är öppen. Stack trace området visas ett meddelande och många av objekten i stackspårningen är inte tillgängliga.

Om det finns information om vissa objekt kanske länkar, men lösningen information objektet fortfarande att vara synlig.

Om du klickar på hyperlänken ska du gå till platsen för den valda metoden i din kod. Det kan finnas en skillnad i versionsnumret, men funktionen att gå till rätt version av koden, kommer i efterföljande versioner.

![Klicka på information om undantag](./media/release-notes-vsix/jumptocode.png)

### <a name="new-entry-points-to-the-search-experience-in-solution-explorer"></a>Ny startpunkter till Search användarupplevelsen i Solution Explorer
Du kan nu komma åt Search via Solution Explorer.

![Sök i Solution Explorer](./media/release-notes-vsix/searchentry.png)

### <a name="displays-a-notification-when-publish-is-completed"></a>Visar ett meddelande när publicerar har slutförts
Ett popup-dialogruta när projektet har publicerats online, så att du kan visa din Application Insights-data i produktion.

![Publicera meddelande om slutförd](./media/release-notes-vsix/publishtoast.png)

## <a name="version-40"></a>Version 4.0
### <a name="search-application-insights-data-from-within-visual-studio"></a>Sök Application Insights-data från Visual Studio
Som sökfunktionen i Application Insights-portalen nu i Visual Studio kan du filtrera och söka på händelsetyper, värden och text och sedan granska enskilda händelser.

![Sökfönster](./media/release-notes-vsix/search.png)

### <a name="see-data-coming-from-your-local-computer-in-diagnostic-tools"></a>Se data som kommer från den lokala datorn i diagnostikverktyg
Du kan visa din telemetri, utöver andra felsökning data på sidan diagnostikverktyg för Visual Studio. Endast ASP.NET 4.5 stöds.

![Diagnostiska verktyg-sidan](./media/release-notes-vsix/diagtools.png)

### <a name="add-the-sdk-to-your-project-without-signing-in-to-azure"></a>Lägg till SDK: N i projektet utan att logga in till Azure
Du behöver inte längre logga in på Azure för att lägga till Application Insights-paket i projektet, antingen via den **nytt projekt** dialogrutan eller från snabbmenyn för projektet. Om du loggar in, kommer SDK: N installeras och konfigureras för att skicka telemetri till portalen som innan. Om du inte har loggat in SDK kommer att läggas till ditt projekt och det genererar telemetri för hubben som diagnostik. Du kan konfigurera den senare om du vill.

![Dialogrutan Nytt projekt](./media/release-notes-vsix/newproject.png)

