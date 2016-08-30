<properties
    pageTitle="Application Insights för JavaScript-webbappar | Microsoft Azure"
    description="Hämta sidvisnings- och sessionsantal, webbklientdata och spåra användningsmönster. Identifiera undantag och prestandaproblem på JavaScript-baserade webbsidor."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/18/2016"
    ms.author="awills"/>

# Application Insights för webbsidor


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Visa prestanda och användning för webbsidor eller appar. Lägg till Visual Studio Application Insights i webbsidans skript och visa information om tider för sidinläsningar och AJAX-anrop, information om och antalet webbläsarundantag och AJAX-fel, samt information om antalet användare och sessioner. Allt detta kan visas efter sida, klientoperativsystem- och webbläsarversion, geografisk plats och andra dimensioner. Du kan också ställa in varningar för antal fel eller långsam sidinläsning.

Du kan använda Application Insights med valfria webbsidor – du lägger bara till ett stycke JavaScript-kod. Om webbtjänsten är [Java](app-insights-java-get-started.md) eller [ASP.NET](app-insights-asp-net.md) kan du integrera telemetri från dina servrar och klienter.

Du behöver en prenumeration på [Microsoft Azure](https://azure.com). Om ditt team har en organisationsprenumeration ber du ägaren att lägga till ditt Microsoft-Account till den. Det finns en kostnadsfri prisnivå, så utveckling och småskalig användning kostar inget.


## Konfigurera Application Insights för din webbsida

Du kanske redan har det. Om din app är ett nytt ASP.NET-projekt och du valde att lägga till Application Insights i dialogrutan Nytt projekt i Visual Studio så har skriptet redan lagts till och allt är klart.

Om inte måste du lägga till ett kodfragment till dina webbsidor genom att följa anvisningarna nedan.

### Öppna en Application Insights-resurs

Application Insights-resursen är den plats där data om sidans prestanda och användning visas. 

Logga in på [Azure-portalen](https://portal.azure.com).

Om du redan har konfigurerat övervakning för serversidan i din app så har du redan en resurs:

![Välj Bläddra, Utvecklartjänster, Application Insights.](./media/app-insights-javascript/01-find.png)

Om du inte redan har en resurs skapar du en:

![Välj Nytt, Utvecklartjänster, Application Insights.](./media/app-insights-javascript/01-create.png)


*Har du redan frågor?* [Mer information om hur du skapar en resurs](app-insights-create-new-resource.md).


### Lägga till SDK-skriptet till appen eller webbsidorna

Hämta skriptet för webbsidor i Snabbstart:

![På översiktsbladet för appen väljer du Snabbstart, Hämta kod för att övervaka webbplatser. Kopiera skriptet.](./media/app-insights-javascript/02-monitor-web-page.png)

Infoga skriptet precis före `<head>`-taggen för alla sidor som du vill spåra. Om din webbplats har en huvudsida kan du placera skriptet där. Till exempel:

* I ett ASP.NET MVC-projekt lägger du till det i `View\Shared\_Layout.cshtml`
* På en SharePoint-plats öppnar du [Webbplatsinställningar/Huvudsida](app-insights-sharepoint.md) på kontrollpanelen.

Skriptet innehåller instrumenteringsnyckeln som dirigerar data till din Application Insights-resurs. 

([Mer detaljerad förklaring av skriptet.](http://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

*(Om du använder ett välkänt ramverk för webbsidor letar du efter adaptrar till Application Insights. Ett exempel är en [AngularJS-modul](http://ngmodules.org/modules/angular-appinsights).)*


## Detaljerad konfiguration

Det finns flera [Parametrar](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) som du kan ange, men i de flesta fall behöver du inte göra det. Du kan till exempel inaktivera eller begränsa antalet Ajax-anrop som rapporteras per sidvy (för att minska trafiken) eller ställa in felsökningsläge så att telemetri snabbt går genom pipelinen utan att skickas i batchar.

Du anger dessa parametrar genom att leta upp den här raden i kodfragmentet och lägga till fler kommaavgränsade poster efter den:

    })({
      instrumentationKey: "..."
      // Insert here
    });

Exempel på [tillgängliga parametrar](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) är:

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: true,

    // Don't log browser exceptions.
    disableExceptionTracking: true,

    // Don't log ajax calls.
    disableAjaxTracking: boolean,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Time page load up to execution of first trackPageView().
    overridePageViewDuration: boolean,

    // Set these dynamically for an authenticated user.
    appUserId: string,
    accountId: string,



## <a name="run"></a>Köra appen

Kör webbappen, använd den en stund för att generera telemetri och vänta några sekunder. Du kan antingen köra den genom att trycka på **F5** på utvecklingsdatorn, eller publicera den och låta användarna använda den.

Om du vill kontrollera telemetrin som en webbapp skickar till Application Insights använder du webbläsarens felsökningsverktyg (**F12** i många webbläsare). Informationen skickas till dc.services.visualstudio.com.

## Granska informationen om webbläsarens prestanda

Öppna bladet Webbläsare om du vill visa aggregerade data från användarnas webbläsare.

![Öppna appens resurs på portal.azure.com och klicka på Inställningar, Webbläsare.](./media/app-insights-javascript/03.png)


*Ser du inga data än? Klicka på **Uppdatera** längst upp på sidan. Ser du fortfarande ingenting? Mer information finns i [Felsökning](app-insights-troubleshoot-faq.md).*

Bladet Webbläsare är ett [Metrics Explorer-blad](app-insights-metrics-explorer.md) med förinställda filter och diagraminställningar. Du kan redigera tidsintervallet, filtren och diagramkonfigurationen om du vill och spara resultatet som en favorit. Klicka på **Återställ standardvärden** för att återgå till det ursprungliga konfigurationsbladet.

## Sidinläsningsprestanda

Längst upp på sidan finns ett segmenterat diagram över sidinläsningstider. Diagrammets totala höjd representerar den genomsnittliga tid det tar att läsa in och visa sidor från appen i användarnas webbläsare. Tiden mäts från tidpunkten då webbläsaren skickar den första HTTP-begäran tills alla synkrona belastningshändelser har bearbetats, inklusive layout och skriptkörning. De omfattar inte asynkrona åtgärder, till exempel inläsning av webbdelar från AJAX-anrop.

I diagrammet delas den totala sidinläsningstiden upp baserat på [standardtiderna som definierats av W3C](http://www.w3.org/TR/navigation-timing/#processing-model). 

![](./media/app-insights-javascript/08-client-split.png)

Observera att *nätverksanslutningstiden* ofta är lägre än förväntat eftersom det är ett medelvärde över alla förfrågningar från webbläsaren till servern. Många enskilda förfrågningar har anslutningstiden 0 eftersom det redan finns en aktiv anslutning till servern.

### Är inläsningen långsam?

Långsamma sidinläsningar är ytterst frustrerande för användarna. Om diagrammet visar på långsamma sidinläsningar är det enkelt att undersöka varför.

Diagrammet visar medelvärdet av alla sidinläsningar i appen. Om du vill se om problemet är begränsat till specifika sidor går du längre ned i bladet, där du ser ett rutnät uppdelat efter sid-URL:

![](./media/app-insights-javascript/09-page-perf.png)

Notera antalet sidvisningar och standardavvikelsen. Om sidantalet är mycket lågt så påverkas inte användarna särskilt mycket av problemet. En hög standardavvikelse (jämförbar med genomsnittet) anger en stor avvikelse mellan enskilda mått.

**Zooma in en URL och en sidvisning.** Klicka på ett sidnamn för att visa ett blad med webbläsardiagram filtrerade på just den URL:en, och sedan på en instans av en sidvy.

![](./media/app-insights-javascript/35.png)

Klicka på `...` om du vill visa en fullständig lista över egenskaper för händelsen eller granska Ajax-anropen och relaterade händelser. Långsamma Ajax-anrop påverkar den allmänna sidinläsningstiden om de är synkrona. Exempel på relaterade händelser är serverbegäranden för samma URL (om du har konfigurerat Application Insights på webbservern).


**Sidprestanda över tid.** Ändra rutnätet Inläsningstid för sidvisning på bladet Webbläsare till ett linjediagram och se om det förekommit toppar vid specifika tidpunkter:

![Klicka i sidhuvudet i rutnätet och välj en annan diagramtyp.](./media/app-insights-javascript/10-page-perf-area.png)

**Ändra uppdelningen baserat på andra dimensioner.** Kanske tar det längre tid att läsa in sidorna i en viss webbläsare, i ett visst klientoperativsystem eller på en viss användarplats? Lägg till ett nytt diagram och experimentera med dimensionen **Gruppera efter**.

![](./media/app-insights-javascript/21.png)


## AJAX-prestanda

Kontrollera att alla AJAX-anrop på dina webbsidor fungerar som de ska. De används ofta för att fylla delar av sidan asynkront. Även om den övergripande sidan kan läsas in snabbt kan användarna bli frustrerade om de öppnar nya webbdelar och måste vänta på att data ska visas i dem.

AJAX-anrop som görs från din webbsida visas på bladet Webbläsare som beroenden.

Du hittar sammanfattningsdiagram i den övre delen av bladet:

![](./media/app-insights-javascript/31.png)

Och detaljerade rutnät längre ned:

![](./media/app-insights-javascript/33.png)

Klicka på en rad om du vill visa specifik information.


> [AZURE.NOTE] Om du tar bort filtret Webbläsare på bladet tas både server- och AJAX-beroenden med i dessa diagram. Klicka på Återställ standardvärden om du vill konfigurera om filtret.

**Gå till misslyckades Ajax-anrop** genom att bläddra ned till rutnätet över beroendefel och klicka på en rad för att visa specifika instanser.

![](./media/app-insights-javascript/37.png)

Klicka på `...` om du vill visa fullständig telemetri om ett Ajax-anrop.

### Rapporteras inga Ajax-anrop?

AJAX-anrop omfattar alla HTTP-anrop som görs från skriptet på din webbsida. Om de inte har rapporterats kontrollerar du att kodfragmentet inte innehåller parametern `disableAjaxTracking` eller `maxAjaxCallsPerView`[](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config).

## Webbläsarundantag

Bladet Webbläsare innehåller ett sammanfattningsdiagram över undantag och ett rutnät med undantagstyper längre ned.

![](./media/app-insights-javascript/39.png)

Om du inte ser webbläsarundantag kontrollerar du att kodfragmentet inte innehåller parametern `disableExceptionTracking`[](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config).

## Granska enskilda sidvisningshändelser

Vanligtvis analyseras telemetri för sidvisningar av Application Insights och du ser endast kumulativa rapporter, som ett genomsnitt av alla användare. Men för felsökningsändamål kan du även titta på enskilda sidvisningshändelser.

Ställ in Filter till Sidvy på bladet Diagnostiksökning.

![](./media/app-insights-javascript/12-search-pages.png)

Välj en händelse om du vill visa mer information. Klicka på ”...” på detaljsidan om du vill visa ännu fler detaljer.

> [AZURE.NOTE] Om du använder [Sök](app-insights-diagnostic-search.md) är det viktigt att du matchar hela ord: ”Info” och ”formation” matchar inte ”Information”, men det gör ”Info*”. Tänk också på att du inte kan börja en sökterm med jokertecken. Exempelvis returnerar en sökning efter ”*formation” inte ”Information”.

> [Lär dig mer om diagnostiksökning](app-insights-diagnostic-search.md)

### Egenskaper för sidvisning

* **Sidvisningens varaktighet** 

 * Som standard den tid det tar att läsa in sidan, från klientbegäran till full inläsning (inklusive extra filer men exklusive asynkrona åtgärder som Ajax-anrop). 
 * Intervallet mellan klientbegäran till körningen av den första `trackPageView` om du anger `overridePageViewDuration` i [sidkonfigurationen](#detailed-configuration). Om du har flyttat trackPageView från dess normala position efter initieringen av skriptet visas ett annat värde.
 * Om `overridePageViewDuration` har angetts och ett varaktighetsargument anges i `trackPageView()`-anropet så används argumentvärdet i stället. 


## Anpassade sidräkningar

Som standard ökar sidräkningen varje gång en ny sida läses in i webbläsaren.  Men du kanske vill räkna fler slags sidvisningar. En sida kan till exempel visa innehåll på flikar, och du kanske vill att en sida ska räknas när användaren byter flik. Eller så kanske JavaScript-kod på sidan läser in nytt innehåll utan att webbläsarens URL ändras.

Infoga ett JavaScript-anrop som det här på lämpligt ställe i klientkoden:

    appInsights.trackPageView(myPageName);

Sidans namn kan innehålla samma tecken som en URL, men allt efter ”#” eller ”?” ignoreras.



## Användningsspårning


Vill du veta vad användarna gör med din app?

* [Lär dig mer om användningsspårning](app-insights-web-track-usage.md)
* [Lär dig mer om API:er för mätvärden och anpassade händelser](app-insights-api-custom-events-metrics.md).


#### <a name="video"></a> Video: Spåra användning

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Nästa steg

* [Spåra användning](app-insights-web-track-usage.md)
* [Anpassade händelser och mätvärden](app-insights-api-custom-events-metrics.md)
* [Skapa – mät – lär](app-insights-overview-usage.md)





<!--HONumber=jun16_HO2-->


