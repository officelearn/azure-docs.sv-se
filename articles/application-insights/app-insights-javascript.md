---
title: "Azure Application Insights för JavaScript-webbappar | Microsoft Docs"
description: "Hämta sidvisnings- och sessionsantal, webbklientdata och spåra användningsmönster. Identifiera undantag och prestandaproblem på JavaScript-baserade webbsidor."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 3b710d09-6ab4-4004-b26a-4fa840039500
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: c4e1ecc824b09fd7523cdc7a29559adca19488f3
ms.lasthandoff: 03/16/2017


---
# <a name="application-insights-for-web-pages"></a>Application Insights för webbsidor
Visa prestanda och användning för webbsidor eller appar. Om du lägger till [Application Insights](app-insights-overview.md) i webbsidans skript så visas information om tider för sidinläsningar och AJAX-anrop, information om och antalet webbläsarundantag och AJAX-fel, samt information om antalet användare och sessioner. Allt detta kan visas efter sida, klientoperativsystem- och webbläsarversion, geografisk plats och andra dimensioner. Du kan ställa in varningar för antal fel eller långsam sidinläsning. Och genom att infoga spårning av anrop i JavaScript-kod kan du spåra hur olika funktioner i ditt webbsideprogram används.

Application Insights kan användas med alla webbsidor – du lägger bara till ett stycke JavaScript-kod. Om webbtjänsten är [Java](app-insights-java-get-started.md) eller [ASP.NET](app-insights-asp-net.md) kan du integrera telemetri från dina servrar och klienter.

![Öppna appens resurs på portal.azure.com och klicka på Webbläsare](./media/app-insights-javascript/03.png)

Du behöver en prenumeration på [Microsoft Azure](https://azure.com). Om ditt team har en organisationsprenumeration ber du ägaren att lägga till ditt Microsoft-Account till den. Utveckling och småskalig användning kostar inget.

## <a name="set-up-application-insights-for-your-web-page"></a>Konfigurera Application Insights för din webbsida
Lägg till inläsningen av kodfragmentet i dina webbsidor enligt följande.

### <a name="open-or-create-application-insights-resource"></a>Öppna eller skapa en Application Insights-resurs
Application Insights-resursen är den plats där data om sidans prestanda och användning visas. 

Logga in på [Azure-portalen](https://portal.azure.com).

Om du redan har konfigurerat övervakning för serversidan i din app så har du redan en resurs:

![Välj Bläddra, Utvecklartjänster, Application Insights.](./media/app-insights-javascript/01-find.png)

Om du inte redan har en resurs skapar du en:

![Välj Nytt, Utvecklartjänster, Application Insights.](./media/app-insights-javascript/01-create.png)

*Har du redan frågor?* [Mer information om hur du skapar en resurs](app-insights-create-new-resource.md).

### <a name="add-the-sdk-script-to-your-app-or-web-pages"></a>Lägga till SDK-skriptet till appen eller webbsidorna
Hämta skriptet för webbsidor i Snabbstart:

![På översiktsbladet för appen väljer du Snabbstart, Hämta kod för att övervaka webbplatser. Kopiera skriptet.](./media/app-insights-javascript/02-monitor-web-page.png)

Infoga skriptet precis före `</head>`-taggen för alla sidor som du vill spåra. Om din webbplats har en huvudsida kan du placera skriptet där. Exempel:

* I ett ASP.NET MVC-projekt lägger du till det i `View\Shared\_Layout.cshtml`
* På en SharePoint-plats öppnar du [Webbplatsinställningar/Huvudsida](app-insights-sharepoint.md) på kontrollpanelen.

Skriptet innehåller instrumenteringsnyckeln som dirigerar data till din Application Insights-resurs. 

([Mer detaljerad förklaring av skriptet.](http://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

*(Om du använder ett välkänt ramverk för webbsidor letar du efter adaptrar till Application Insights. Det finns till exempel [en AngularJS-modul](http://ngmodules.org/modules/angular-appinsights).)*

## <a name="detailed-configuration"></a>Detaljerad konfiguration
Det finns flera [Parametrar](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) som du kan ange, men i de flesta fall behöver du inte göra det. Du kan t.ex. inaktivera eller begränsa antalet Ajax-anrop som rapporteras per sidvy (om du vill minska trafiken). Eller så kan du ställa in felsökningsläge så att telemetrin rör sig snabbt genom pipelinen utan att batchhanteras.

Du anger dessa parametrar genom att leta upp den här raden i kodfragmentet och lägga till fler kommaavgränsade poster efter den:

    })({
      instrumentationKey: "..."
      // Insert here
    });

Exempel på [tillgängliga parametrar](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) är:

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: boolean,

    // Don't log browser exceptions.
    disableExceptionTracking: boolean,

    // Don't log ajax calls.
    disableAjaxTracking: boolean,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Time page load up to execution of first trackPageView().
    overridePageViewDuration: boolean,

    // Set these dynamically for an authenticated user.
    appUserId: string,
    accountId: string,



## <a name="run"></a>Kör din app
Kör webbappen, använd den en stund för att generera telemetri och vänta några sekunder. Du kan antingen köra den genom att trycka på **F5** på utvecklingsdatorn, eller publicera den och låta användarna använda den.

Om du vill kontrollera telemetrin som en webbapp skickar till Application Insights använder du webbläsarens felsökningsverktyg (**F12** i många webbläsare). Informationen skickas till dc.services.visualstudio.com.

## <a name="explore-your-browser-performance-data"></a>Granska informationen om webbläsarens prestanda
Öppna bladet Webbläsare om du vill visa aggregerade data från användarnas webbläsare.

![Öppna appens resurs på portal.azure.com och klicka på Inställningar, Webbläsare.](./media/app-insights-javascript/03.png)

*Ser du inga data än? Klicka på**Uppdatera**längst upp på sidan. Ser du fortfarande ingenting? Mer information finns i [Felsökning](app-insights-troubleshoot-faq.md).*

Bladet Webbläsare är ett [Metrics Explorer-blad](app-insights-metrics-explorer.md) med förinställda filter och diagraminställningar. Du kan redigera tidsintervallet, filtren och diagramkonfigurationen om du vill och spara resultatet som en favorit. Klicka på **Återställ standardvärden** för att återgå till det ursprungliga konfigurationsbladet.

## <a name="page-load-performance"></a>Sidinläsningsprestanda
Längst upp på sidan finns ett segmenterat diagram över sidinläsningstider. Diagrammets totala höjd representerar den genomsnittliga tid det tar att läsa in och visa sidor från appen i användarnas webbläsare. Tiden mäts från tidpunkten då webbläsaren skickar den första HTTP-begäran tills alla synkrona belastningshändelser har bearbetats, inklusive layout och skriptkörning. De omfattar inte asynkrona åtgärder, till exempel inläsning av webbdelar från AJAX-anrop.

I diagrammet delas den totala sidinläsningstiden upp baserat på [standardtiderna som definierats av W3C](http://www.w3.org/TR/navigation-timing/#processing-model). 

![](./media/app-insights-javascript/08-client-split.png)

Observera att *nätverksanslutningstiden* ofta är lägre än förväntat eftersom det är ett medelvärde över alla förfrågningar från webbläsaren till servern. Många enskilda förfrågningar har anslutningstiden 0 eftersom det redan finns en aktiv anslutning till servern.

### <a name="slow-loading"></a>Är inläsningen långsam?
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

## <a name="ajax-performance"></a>AJAX-prestanda
Kontrollera att alla AJAX-anrop på dina webbsidor fungerar som de ska. De används ofta för att fylla delar av sidan asynkront. Även om den övergripande sidan kan läsas in snabbt kan användarna bli frustrerade om de öppnar nya webbdelar och måste vänta på att data ska visas i dem.

AJAX-anrop som görs från din webbsida visas på bladet Webbläsare som beroenden.

Du hittar sammanfattningsdiagram i den övre delen av bladet:

![](./media/app-insights-javascript/31.png)

Och detaljerade rutnät längre ned:

![](./media/app-insights-javascript/33.png)

Klicka på en rad om du vill visa specifik information.

> [!NOTE]
> Om du tar bort filtret Webbläsare på bladet tas både server- och AJAX-beroenden med i dessa diagram. Klicka på Återställ standardvärden om du vill konfigurera om filtret.
> 
> 

**Gå till misslyckades Ajax-anrop** genom att bläddra ned till rutnätet över beroendefel och klicka på en rad för att visa specifika instanser.

![](./media/app-insights-javascript/37.png)


Klicka på `...` om du vill visa fullständig telemetri om ett Ajax-anrop.

### <a name="no-ajax-calls-reported"></a>Rapporteras inga Ajax-anrop?
AJAX-anrop omfattar alla HTTP/HTTPS-anrop som görs från skriptet på din webbsida. Om de inte har rapporterats kontrollerar du att kodfragmentet inte innehåller [parametern](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableAjaxTracking` eller `maxAjaxCallsPerView` .

## <a name="browser-exceptions"></a>Webbläsarundantag
Bladet Webbläsare innehåller ett sammanfattningsdiagram över undantag och ett rutnät med undantagstyper längre ned.

![](./media/app-insights-javascript/39.png)

Om du inte ser webbläsarundantag kontrollerar du att kodfragmentet inte innehåller parametern `disableExceptionTracking`[](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config).

## <a name="inspect-individual-page-view-events"></a>Granska enskilda sidvisningshändelser

Vanligtvis analyseras telemetri för sidvisningar av Application Insights och du ser endast kumulativa rapporter, som ett genomsnitt av alla användare. Men för felsökningsändamål kan du även titta på enskilda sidvisningshändelser.

Ställ in Filter till Sidvy på bladet Diagnostiksökning.

![](./media/app-insights-javascript/12-search-pages.png)

Välj en händelse om du vill visa mer information. Klicka på ”...” på detaljsidan om du vill visa ännu fler detaljer.

> [!NOTE]
> Om du använder [Sök](app-insights-diagnostic-search.md) är det viktigt att du matchar hela ord: ”Info” och ”formation” matchar inte ”Information”.
> 
> 

Du kan också använda de kraftfulla [Analytics-frågespråket](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-tour#browser-timings-table) när du söker efter sidvyer.

### <a name="page-view-properties"></a>Egenskaper för sidvisning
* **Sidvisningens varaktighet** 
  
  * Som standard den tid det tar att läsa in sidan, från klientbegäran till full inläsning (inklusive extra filer men exklusive asynkrona åtgärder som Ajax-anrop). 
  * Intervallet mellan klientbegäran till körningen av den första `trackPageView` om du anger `overridePageViewDuration` i [sidkonfigurationen](#detailed-configuration). Om du har flyttat trackPageView från dess normala position efter initieringen av skriptet visas ett annat värde.
  * Om du har angett `overridePageViewDuration` och ett varaktighetsargument anges i `trackPageView()`-anropet, så används argumentvärdet istället. 

## <a name="custom-page-counts"></a>Anpassade sidräkningar
Som standard ökar sidräkningen varje gång en ny sida läses in i webbläsaren.  Men du kanske vill räkna fler slags sidvisningar. En sida kan till exempel visa innehåll på flikar, och du kanske vill att en sida ska räknas när användaren byter flik. Eller så kanske JavaScript-kod på sidan läser in nytt innehåll utan att webbläsarens URL ändras.

Infoga ett JavaScript-anrop som det här på lämpligt ställe i klientkoden:

    appInsights.trackPageView(myPageName);

Sidans namn kan innehålla samma tecken som en URL, men allt efter ”#” eller ”?” ignoreras.

## <a name="usage-tracking"></a>Användningsspårning
Vill du veta vad användarna gör med din app?

* [Lär dig mer om användningsspårning](app-insights-web-track-usage.md)
* [Lär dig mer om API:er för mätvärden och anpassade händelser](app-insights-api-custom-events-metrics.md).

## <a name="video"></a> Video


> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]



## <a name="next"></a>Nästa steg
* [Spåra användning](app-insights-web-track-usage.md)
* [Anpassade händelser och mätvärden](app-insights-api-custom-events-metrics.md)
* [Skapa – mät – lär](app-insights-overview-usage.md)


