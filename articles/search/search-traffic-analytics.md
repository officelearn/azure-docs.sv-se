---
title: implementera Sök trafik analys – Azure Search
description: Aktivera Sök trafik analys för Azure Search för att lägga till telemetri och händelser som initieras av användaren för att logga filer.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb12ed2f18df100ab3f679e7a8a3ef1e7c1aca45
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647801"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Implementera Sök trafik analys i Azure Search
Sök trafik analys är ett mönster för att implementera en feedback-slinga för din Sök tjänst. Det här mönstret beskriver nödvändiga data och hur du samlar in dem med hjälp av Application Insights, en bransch ledare för att övervaka tjänster på flera plattformar.

Med Sök trafik analys kan du få insyn i Sök tjänsten och låsa upp insikter om dina användare och deras beteende. Genom att ha data om vad dina användare väljer, är det möjligt att fatta beslut som ytterligare förbättrar din Sök upplevelse och att du kan använda dem när resultatet inte är förväntat.

Azure Search erbjuder en telemetri-lösning som integrerar Azure Application insikter och Power BI för att ge djupgående övervakning och spårning. Eftersom interaktion med Azure Search bara via API: er, måste telemetri implementeras av utvecklarna med hjälp av Sök funktionen på den här sidan.

## <a name="identify-relevant-search-data"></a>Identifiera relevanta Sök data

Om du vill ha användbara Sök mått är det nödvändigt att logga vissa signaler från användarna av sökprogrammet. Dessa signaler indikerar innehåll som användarna är intresserade av och som de anser vara relevanta för deras behov.

Det finns två Signals öknings Trafikanalys behov:

1. Användare som genererade Sök händelser: endast Sök frågor som initierats av en användare är intressanta. Sök begär Anden som används för att fylla i FACET, ytterligare innehåll eller intern information, är inte viktiga och de skevar och gör resultatet av dina resultat.

2. Användaren skapade klicknings händelser: När du klickar på det här dokumentet refererar vi till en användare som väljer ett visst Sök resultat som returneras från en Sök fråga. Ett klick innebär vanligt vis att ett dokument är ett relevant resultat för en speciell Sök fråga.

Genom att länka sökningen och klicka på händelser med ett korrelations-ID, är det möjligt att analysera beteenden för användare i ditt program. Det går inte att hämta de här Sök insikterna med Sök trafik loggar.

## <a name="add-search-traffic-analytics"></a>Lägg till Sök trafik analys

Signalerna som anges i föregående avsnitt måste samlas in från Sök programmet när användaren interagerar med den. Application Insights är en utöknings bar övervaknings lösning som är tillgänglig för flera plattformar med flexibla instrument alternativ. Med hjälp av Application Insights kan du dra nytta av Power BI Sök rapporter som skapats av Azure Search för att göra det enklare att analysera data.

På [Portal](https://portal.azure.com) sidan för din Azure Search-tjänst innehåller bladet Sök trafikanalys ett lathund-blad för följande telemetri-mönster. Du kan också välja eller skapa en Application Insights resurs och se nödvändiga data på ett och samma ställe.

![Sök Trafikanalys-instruktioner][1]

## <a name="1---select-a-resource"></a>1 – Välj en resurs

Du måste välja en Application Insights resurs som du vill använda eller skapa en om du inte redan har en. Du kan använda en resurs som redan används för att logga de anpassade händelserna som krävs.

När du skapar en ny Application Insights resurs, är alla program typer giltiga för det här scenariot. Välj den som passar bäst för den plattform som du använder.

Du behöver Instrumentation-nyckeln för att skapa telemetri-klienten för ditt program. Du kan hämta den från instrument panelen för Application Insights portalen, eller så kan du hämta den från sidan Sök Trafikanalys och välja den instans som du vill använda.

## <a name="2---add-instrumentation"></a>2 – Lägg till instrumentering

I den här fasen kan du skapa ett eget sökprogram med hjälp av Application Insights resurs som du skapade i steget ovan. Det finns fyra steg i den här processen:

**Steg 1: Skapa en telemetri-** klient detta är det objekt som skickar händelser till Application Insights resursen.

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

För andra språk och plattformar, se den fullständiga [listan](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**Steg 2: Begär ett Sök-ID för** korrelation att korrelera Sök begär Anden med klick, det är nödvändigt att ha ett korrelations-ID som relaterar till dessa två distinkta händelser. Azure Search ger dig ett Sök-ID när du begär det med ett huvud:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
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

**Steg 3: Loggs öknings händelser**

Varje gång en sökbegäran utfärdas av en användare bör du logga in som en Sök händelse med följande schema på en Application Insights anpassad händelse:

**SearchServiceName**: (sträng) Sök tjänst namn **SearchId**: (GUID) unik identifierare för Sök frågan (kommer i Sök svaret) **IndexName**: (sträng) Sök tjänst index som ska frågas **QueryTerms**: (sträng) Sök villkor som anges av användaren **resultcount som**: (int) antalet dokument som returnerades (kommer i Sök svaret) **ScoringProfile**: (sträng) för den bedömnings profil som används, om något

> [!NOTE]
> Antal begär Anden för användardefinierade frågor genom att lägga till $count = true i Sök frågan. Mer information finns [här](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Kom ihåg att endast logga Sök frågor som genereras av användarna.
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

**Steg 4: Logga klicknings händelser**

Varje gång en användare klickar på ett dokument, är det en signal som måste loggas för söknings analys. Använd Application Insights anpassade händelser för att logga dessa händelser med följande schema:

**ServiceName**: (sträng) Sök tjänst namn **SearchId**: (GUID) unik identifierare för den relaterade Sök frågan **fulltextdokument**: (sträng) dokument identifierare **position**: (int) rangordning av dokumentet på sidan med Sök Resultat

> [!NOTE]
> Position syftar på kardinal ordningen i ditt program. Du är kostnads fri att ange det här talet, så länge det alltid är detsamma, för att möjliggöra jämförelse.
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

## <a name="3---analyze-in-power-bi"></a>3-analysera i Power BI

När du har instrumenterat appen och kontrollerat att programmet är korrekt anslutet till Application Insights kan du använda en fördefinierad mall som skapats av Azure Search för Power BI Skriv bordet. 

Azure Search tillhandahåller ett övervaknings [Power BI innehålls paket](https://app.powerbi.com/getdata/services/azure-search) så att du kan analysera logg data. Innehålls paketet innehåller fördefinierade diagram och tabeller som är användbara för att analysera ytterligare data som registrerats för Sök trafik analys. Mer information finns i den [hjälpsidan för Innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. I rutan Azure Search instrument panelens vänstra navigerings fönster, under **Inställningar**, klickar du på **Sök trafik analys**.

2. På sidan **Sök trafik analys** går du till steg 3 och klickar på **Hämta Power BI Desktop** för att installera Power BI.

   ![Hämta Power BI rapporter](./media/search-traffic-analytics/get-use-power-bi.png "Hämta Power BI rapporter")

2. Klicka på **Hämta PowerBI rapport**på samma sida.

3. Rapporten öppnas i Power BI Desktop och du uppmanas att ansluta till Application Insights. Du hittar den här informationen på sidan Azure Portal för att Application Insights resursen.

   ![Anslut till Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Anslut till Application Insights")

4. Klicka på **Läs in**.

Rapporten innehåller diagram och tabeller som hjälper dig att fatta mer välgrundade beslut för att förbättra Sök prestanda och relevans.

Måtten omfattar följande objekt:

* Klicka igenom Rate (prod) (pris): förhållandet mellan användare som klickar på ett speciellt dokument för att summera antalet sökningar.
* Sökningar utan klickningar: villkor för de vanligaste frågorna som registrerar inga klick
* De mest klickade dokumenten: de mest klickade dokumenten efter ID under de senaste 24 timmarna, 7 dagar och 30 dagar.
* Populära term dokument par: termer som resulterar i samma dokument som du klickar på, sorteras efter klickningar.
* Tid att klicka: klickar på Bucket efter tid sedan Sök frågan

Följande skärm bild visar inbyggda rapporter och diagram för analys av Sök trafik analys.

![Power BI instrument panel för Azure Search](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Power BI instrument panel för Azure Search")

## <a name="next-steps"></a>Nästa steg
Instrumentera ditt sökprogram för att få kraftfull och insiktad information om din Sök tjänst.

Du hittar mer information om [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) och besöker sidan med [priser](https://azure.microsoft.com/pricing/details/application-insights/) för att lära dig mer om deras olika tjänst nivåer.

Lär dig mer om att skapa fantastiska rapporter. Se [komma igång med Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) information

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
