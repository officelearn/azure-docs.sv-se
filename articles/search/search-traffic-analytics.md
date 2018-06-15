---
title: Sök trafik Analytics för Azure Search | Microsoft Docs
description: Aktivera sökning trafik analytics för Azure Search molnbaserade search-tjänsten på Microsoft Azure för att låsa upp insikter om dina användare och dina data.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2017
ms.author: heidist
ms.openlocfilehash: 4b40e8c9f681b7489c0ab2ffe7b369cc869c73e2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777267"
---
# <a name="what-is-search-traffic-analytics"></a>Vad är Sök trafik analytics
Sök trafik analytics är ett mönster för att implementera en feedback-slinga för din söktjänst. Det här mönstret beskriver nödvändiga data och hur du samlar in den med hjälp av Application Insights, marknadsledande för övervakning av tjänster på flera plattformar.

Sök trafik analytics kan du få insyn i din söktjänst och låsa upp insikter om dina användare och deras beteende. Genom att data om vad användarna väljer, är det möjligt att ytterligare förbättrar dina sökinställningar beslut och säkerhetskopiering av när resultatet är vad förväntades inte.

Azure Search ger en telemetri som integrerar Azure Application Insights och Power BI för att tillhandahålla detaljerad övervakning och spårning. Eftersom interaktion med Azure Search är endast via API: er, implementeras telemetrin med utvecklare sökning, följa anvisningarna i den här sidan.

## <a name="identify-the-relevant-search-data"></a>Identifiera relevanta sökdata

Om du vill att användbar sökning mått är det nödvändigt att logga vissa signaler från användarna sökprogram. Dessa signaler tillkännage innehåll som användare är intresserad av och de anser relevanta för deras behov.

Det finns två signaler Sök trafik Analytics måste:

1. Användargenererat Sök händelser: endast sökfrågor som initierats av en användare är intressant. Search-begäranden används för att fylla facets ytterligare innehåll eller interna information, är inte viktiga och ge skeva och bias dina resultat.

2. Användargenererat på händelser: klick i det här dokumentet vi hänvisa till en användare att välja ett särskilt sökresultat som har returnerats från en sökfråga. Klicka innebär vanligtvis att dokumentet är ett resultat som är relevanta för en specifik sökfråga.

Genom att länka sökningen och klicka på händelser med en Korrelations-id, är det möjligt att analysera beteenden för användare i ditt program. Dessa Sök insikter är omöjligt att få med endast trafik sökloggar.

## <a name="how-to-implement-search-traffic-analytics"></a>Hur du implementerar Sök trafik analytics

Signalerar som nämns i föregående avsnitt måste samlas in från sökprogram som användaren interagerar med den. Application Insights är en utökningsbar övervakningslösning, tillgänglig för flera plattformar med flexibel instrumentation alternativ. Användning av Application Insights kan du utnyttja Power BI Sök rapporterna som skapats med Azure Search så att det blir lättare att analysera data.

I den [portal](https://portal.azure.com) för din Azure Search-tjänst, Sök trafik Analytics bladet innehåller en fusklapp för att följa det här mönstret telemetri. Du kan markera eller skapa en Application Insights-resurs och se de nödvändiga data på ett ställe.

![Sök trafik Analytics instruktioner][1]

### <a name="1-select-an-application-insights-resource"></a>1. Välj en Application Insights-resurs

Du måste välja en Application Insights-resurs för att använda eller skapa en om du inte redan har en. Du kan använda en resurs som redan används krävs anpassade händelser.

När du skapar en ny Application Insights-resurs, gäller alla programtyper för det här scenariot. Välj det alternativ som passar bäst för den plattform som du använder.

Du behöver den instrumentation nyckeln för att skapa klienten telemetri för ditt program. Du kan hämta den från instrumentpanelen för Application Insights-portalen eller du hämta det från sidan Sök trafik Analytics att välja den instans som du vill använda.

### <a name="2-instrument-your-application"></a>2. Instrumentera ditt program

Den här fasen är där instrumentera egna sökprogram med Application Insights-resursen din skapade i steget ovan. Det finns fyra steg att den här processen:

**JAG. Skapa en klient telemetri** detta är det objekt som skickar händelser till Application Insights-resurs.

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

För andra språk och plattformar, se hela [listan](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**II. Begära ett Sök-ID för korrelation** för att korrelera search-begäranden med klick, är det nödvändigt att ha en Korrelations-id som är kopplat dessa händelser. Azure Search innehåller Sök-Id när du begär med ett huvud:

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

**III. Sök händelser**

Varje gång som en sökbegäran utfärdas av en användare, bör du logga som som en händelse för sökningen med följande schemat på en anpassad Application Insights-händelse:

**ServiceName**: (sträng) för tjänsten söknamn **SearchId**: (guid) Unik identifierare för frågan (ingår i Sök-svaret) **indexnamn**: (sträng) tjänsten sökindex ska efterfrågade **QueryTerms**: (sträng) sökvillkor som anges av användaren **ResultCount**: (int) antal dokument som returnerades (ingår i Sök-svaret)  **ScoringProfile**: (sträng) namnet på bedömningsprofilen används, i förekommande fall

> [!NOTE]
> Begär antal på användargenererat frågor genom att lägga till $count = true till din fråga. Mer information [här](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
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

**IV. Klicka på Logga händelser**

Varje gång en användare klickar på ett dokument som är en signal som ska loggas för sökningar för analys. Använd anpassade händelser för Application Insights loggar dessa händelser med följande schema:

**ServiceName**: (sträng) för tjänsten söknamn **SearchId**: (guid) Unik identifierare för den relaterade sökfrågan **DocId**: (sträng) dokument-ID **Position**: (int) rangordningen för dokumentet i sökningen resultatsida

> [!NOTE]
> Positionen refererar till den kardinalkurvelementets ordningen i ditt program. Du kan ange det här numret så länge det är alltid detsamma, så att för jämförelse.
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

När du har instrumenterats din app och verifiera ditt program är korrekt ansluten till Application Insights, kan du använda en fördefinierad mall som skapats med Azure Search för Power BI desktop.
Den här mallen innehåller diagram och tabeller som kan hjälper dig att fatta välgrundade beslut att förbättra din sökningsprestanda och relevans.

Du behöver tre uppgifter om Application Insights för att initiera mallen Power BI desktop. Dessa data finns på sidan Sök trafik Analytics när du väljer resursen som ska användas

![Application Insights Data i bladet Sök trafik Analytics][2]

Mått som ingår i Power BI desktop mallen:

*   Klicka på via hastighet (CTR): förhållandet mellan användare och klicka på ett visst dokument med antalet totala sökningar.
*   Söker utan klick: villkoren för vanliga frågor som registrerar några klick
*   Mest klickade dokument: mest klickade på dokument-ID: t i de senaste 24 timmarna, 7 dagar och 30 dagar.
*   Populära termen dokument par: termer som resulterar i samma dokument klickade på, sorterade efter klick.
*   Tid för att klicka på: klick bucketgrupperade av tid sedan frågan

![Power BI-mall för att läsa från Application Insights][3]


## <a name="next-steps"></a>Nästa steg
Instrumentera tillämpningsprogrammet Sök för att hämta kraftfulla och insiktsfulla data om din söktjänst.

Du hittar mer information om Application Insights [här](https://go.microsoft.com/fwlink/?linkid=842905). Besök Application Insights [sida med priser](https://azure.microsoft.com/pricing/details/application-insights/) lära dig mer om de olika tjänstnivåerna.

Mer information om hur du skapar häpnadsväckande rapporter. Se [komma igång med Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/) information

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
