---
title: Söktrafikanalys – Azure Search
description: Aktivera söktrafikanalys för Azure Search, en söktjänst i molnet på Microsoft Azure för att låsa upp insikter om dina användare och dina data.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4cc7434508e49715e95c87421db2bbed7e20de05
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310306"
---
# <a name="what-is-search-traffic-analytics"></a>Vad är search traffic analytics
Söktrafikanalys är ett mönster för att implementera en feedbackloop för din söktjänst. Det här mönstret beskriver uppgifterna som krävs och hur du samlar in den med hjälp av Application Insights, branschledande för övervakningstjänster i flera plattformar.

Söktrafikanalys kan du få insyn i din söktjänst och låsa upp insikter om dina användare och deras beteende. Genom att låta data om vad användarna väljer, är det möjligt att fatta beslut som ytterligare förbättrar din sökning och avbryter när resultatet är inte vad som förväntas.

Azure Search erbjuder ett-telemetrilösning som kan integreras Azure Application Insights och Power BI för att tillhandahålla djupgående övervakning och spårning. Eftersom interaktion med Azure Search är endast via API: er, måste telemetri som implementeras av utvecklare med search, följa anvisningarna i den här sidan.

## <a name="identify-the-relevant-search-data"></a>Identifiera den relevanta sökdata

Det är nödvändigt att logga vissa signaler från användare av search-program för att få användbara mätdata ska. Dessa signaler tillkännage innehåll som användare är intresserad av och de anser relevanta för deras behov.

Det finns två signaler Söktrafikanalys måste:

1. Användargenererade sökhändelser: endast sökfrågor som initierats av en användare är intressant. Sök efter ansökningar som används för att fylla fasetter ytterligare innehåll eller någon intern information är inte viktiga och ge skeva och bias dina resultat.

2. Användargenererade klickar du på händelser: Klick i det här dokumentet kan hänvisa vi till en användare väljer en viss sökresultat som har returnerats från en sökfråga. Ett klick innebär vanligtvis att ett dokument är relevanta resultat för en specifik sökfråga.

Genom att länka Sök och klicka på händelser med ett Korrelations-id, är det möjligt att analysera beteenden för användare på ditt program. Dessa search insikter är omöjligt att få med endast trafik sökloggar.

## <a name="how-to-implement-search-traffic-analytics"></a>Hur du implementerar söktrafikanalys

Signaler som nämns i föregående avsnitt måste samlas in från search-program när användaren interagerar med den. Application Insights är en utökningsbar lösning för övervakning, tillgängliga för flera plattformar, med alternativ för flexibel instrumentation. Användning av Application Insights kan du dra nytta av Power BI-search-rapporter som skapats av Azure Search för att underlätta analyser av data.

I den [portal](https://portal.azure.com) för din Azure Search-tjänst, bladet Search Traffic Analytics innehåller en fusklapp för att kunna följa det här mönstret för telemetri. Du kan också välja eller skapa en Application Insights-resurs och se nödvändiga data, alla på samma plats.

![Sök trafikanalys instruktioner][1]

### <a name="1-select-an-application-insights-resource"></a>1. Välj en Application Insights-resurs

Du måste välja en Application Insights-resurs för att använda eller skapa en om du inte redan har en. Du kan använda en resurs som redan används för att logga in nödvändiga anpassade händelser.

När du skapar en ny Application Insights-resurs, gäller alla programtyper för det här scenariot. Välj det som passar bäst för den plattform som du använder.

Du måste instrumenteringsnyckeln för att skapa telemetriklienten för ditt program. Du kan hämta den från instrumentpanelen i Application Insights-portalen eller du kan hämta det från sidan Search Traffic Analytics att välja den instans som du vill använda.

### <a name="2-instrument-your-application"></a>2. Instrumentera programmet

Den här fasen är där instrumentera din egen search-program med Application Insights-resursen skapade i ovanstående steg. Det finns fyra steg att den här processen:

**JAG. Skapa en telemetriklienten** detta är det objekt som skickar händelser till Application Insights-resurs.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

För övriga språk och plattformar, se hela [lista](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**II. Begär ett Sök-ID för korrelation** för att knyta samman sökförfrågningar med klick, är det nödvändigt att ha ett Korrelations-id som berör dessa händelser. Azure Search har ett Sök-Id när du begär det med en rubrik:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**III. Sökhändelser**

Varje gång som en sökbegäran utfärdas av en användare kan logga du som som en sökning-händelse med följande schema i en anpassad händelse i Application Insights:

**ServiceName**: (sträng) söktjänstnamnet **SearchId**: (guid) Unik identifierare för sökfrågan (det sker i Sök-svaret) **IndexName**: (sträng) tjänsten sökindex vara efterfrågas **QueryTerms**: (sträng) sökvillkor som anges av användaren **resultcount som**: (int) antalet dokument som returnerades (det sker i Sök-svaret)  **ScoringProfile**: (sträng) namnet på den bedömningsprofilen som används, i förekommande fall

> [!NOTE]
> Antal begäranden på användargenererat frågor genom att lägga till $count = SANT om din sökfråga. Läs mer information [här](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Kom ihåg att endast logga sökfrågor som genereras av användare.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**IV. Logga in klickar du på händelser**

Varje gång en användare klickar på ett dokument som är en signal som måste vara inloggad för search-analys. Använd anpassade händelser för Application Insights för att logga dessa händelser med följande schema:

**ServiceName**: (sträng) söktjänstnamnet **SearchId**: (guid) Unik identifierare för relaterade sökfrågan **DocId**: (sträng) dokument-ID **Position**: (int) rangordningen för dokument i sökningen resultatsida

> [!NOTE]
> Position refererar till väsentliga ordningen i ditt program. Du kan ange antalet, så länge det är alltid detsamma, så att jämförelse.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

### <a name="3-analyze-with-power-bi-desktop"></a>3. Analysera med Power BI Desktop

När du har instrumenterade din app och verifierat programmet är korrekt ansluten till Application Insights kan använda du en fördefinierad mall som skapats av Azure Search för Power BI desktop.
Den här mallen innehåller diagram och tabeller som hjälper dig att fatta mer välgrundade beslut för att förbättra din sökningsprestanda och relevans.

Om du vill skapa en instans av Power BI desktop mallen, behöver du tre uppgifter om Application Insights. Dessa data kan hittas på sidan Söktrafikanalys när du väljer resursen som ska användas

![Application Insights-Data i bladet Search Traffic Analytics][2]

Mått som ingår i Power BI desktop mallen:

*   Klicka på via hastighet (CTR): förhållandet mellan användare och klicka på ett visst dokument för hur många totala sökningar.
*   Söker utan klick: villkoren för vanliga frågor som registrerar inga klick
*   Mest klickade dokument: mest klickade dokument efter ID under de senaste 24 timmarna, sju dagar och 30 dagar.
*   Populära termen dokument-par: villkor som resulterar i samma dokument klickade på, sorterade efter klick.
*   Tid för att klicka på: klick bucketas av tiden efter sökfrågan

![Power BI-mall för att läsa från Application Insights][3]


## <a name="next-steps"></a>Nästa steg
Instrumentera din search-program för att få kraftfulla och informationsrika data om din söktjänst.

Du hittar mer information om Application Insights [här](https://go.microsoft.com/fwlink/?linkid=842905). Gå till Application Insights [prissättningssidan](https://azure.microsoft.com/pricing/details/application-insights/) mer information om sina olika tjänstnivåer.

Lär dig mer om att skapa fantastiska rapporter. Se [komma igång med Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/) information

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
