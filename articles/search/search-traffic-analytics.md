---
title: Rapport om Sök trafik analys data
titleSuffix: Azure Cognitive Search
description: Aktivera Sök trafik analys för Azure Kognitiv sökning, samla in telemetri och händelser som initieras av användaren med hjälp av Application Insights och analysera sedan resultaten i en Power BI rapport.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: b9b0ba85aed4d63fe6bb939c9ed3b99d3e789397
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932572"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>Implementera Sök trafik analys i Azure Kognitiv sökning

Sök trafik analys är ett mönster för att implementera en feedback-slinga för din Sök tjänst. Målet är att samla in telemetri om användare som initieras genom att klicka på händelser och tangent bords indata. Med hjälp av den här informationen kan du fastställa effektiviteten hos din Sök lösning, inklusive populära Sök villkor, klickningar på genomklickning och vilka frågor som indata ger noll resultat.

Det här mönstret tar ett beroende på [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (en funktion i [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)) för att samla in användar data. Du måste också lägga till instrumentering till din klient kod enligt beskrivningen i den här artikeln. Slutligen behöver du en rapporterings mekanism för att analysera data. Vi rekommenderar Power BI men du kan använda valfritt verktyg som ansluter till Application Insights.

> [!NOTE]
> Mönstret som beskrivs i den här artikeln är för avancerade scenarier och klick Ströms-data som genereras av klienten. Du kan också rapportera om logg information som genererats av Sök tjänsten. Mer information om tjänst loggs rapporter finns i [övervaka resursförbrukning och fråga aktivitet](search-monitor-usage.md).

## <a name="identify-relevant-search-data"></a>Identifiera relevanta Sök data

Om du vill ha användbara Sök mått är det nödvändigt att logga vissa signaler från användarna av sökprogrammet. Dessa signaler indikerar innehåll som användarna är intresserade av och som de anser vara relevanta för deras behov.

Det finns två Signals öknings Trafikanalys behov:

+ Användar genererade Sök händelser: endast Sök frågor som initieras av en användare är intressanta. Sök begär Anden som används för att fylla i FACET, ytterligare innehåll eller intern information, är inte viktiga och de skevar och gör resultatet av dina resultat.

+ Händelser som genereras av användaren: genom att klicka i det här dokumentet refererar vi till en användare som väljer ett visst Sök resultat som returneras från en Sök fråga. Ett klick innebär vanligt vis att ett dokument är ett relevant resultat för en speciell Sök fråga.

Genom att länka sökningen och klicka på händelser med ett korrelations-ID, är det möjligt att analysera beteenden för användare i ditt program. Det går inte att hämta de här Sök insikterna med Sök trafik loggar.

## <a name="add-search-traffic-analytics"></a>Lägg till Sök trafik analys

Signalerna som anges i föregående avsnitt måste samlas in från Sök programmet när användaren interagerar med den. Application Insights är en utöknings bar övervaknings lösning som är tillgänglig för flera plattformar med flexibla instrument alternativ. Med hjälp av Application Insights kan du dra nytta av Power BI Sök rapporter som skapats av Azure Kognitiv sökning för att göra det enklare att analysera data.

På [Portal](https://portal.azure.com) sidan för din Azure kognitiv sökning-tjänst innehåller sidan Sök trafikanalys ett lathund-blad för följande telemetri-mönster. Du kan också välja eller skapa en Application Insights resurs och se nödvändiga data på ett och samma ställe.

![Sök Trafikanalys-instruktioner][1]

## <a name="1---select-a-resource"></a>1 – Välj en resurs

Du måste välja en Application Insights resurs som du vill använda eller skapa en om du inte redan har en. Du kan använda en resurs som redan används för att logga de anpassade händelserna som krävs.

När du skapar en ny Application Insights resurs, är alla program typer giltiga för det här scenariot. Välj den som passar bäst för den plattform som du använder.

Du behöver Instrumentation-nyckeln för att skapa telemetri-klienten för ditt program. Du kan hämta den från instrument panelen för Application Insights portalen, eller så kan du hämta den från sidan Sök Trafikanalys och välja den instans som du vill använda.

## <a name="2---add-instrumentation"></a>2 – Lägg till instrumentering

I det här steget ska du skapa ett eget sökprogram med hjälp av Application Insights resurs som du skapade i steget ovan. Det finns fyra steg i den här processen:

**Steg 1: skapa en telemetri-klient**

Detta är det objekt som skickar händelser till Application Insights resursen.

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

**Steg 2: begär ett Sök-ID för korrelation**

Om du vill korrelera Sök begär Anden med klick måste du ha ett korrelations-ID som relaterar till dessa två distinkta händelser. Azure Kognitiv sökning ger dig ett Sök-ID när du begär det med ett huvud:

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

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

**Steg 3: Logga Sök händelser**

Varje gång en sökbegäran utfärdas av en användare bör du logga in som en Sök händelse med följande schema på en Application Insights anpassad händelse:

**SearchServiceName**: (sträng) Sök tjänst namn **SearchId**: (GUID) unik identifierare för Sök frågan (kommer i Söksvaret) **IndexName**: (sträng) Sök tjänst index som ska frågas **QueryTerms**: (sträng) Sök villkoren som anges i användar **resultcount som**: (int) antalet dokument som returnerades (kommer i Sök svaret) **ScoringProfile**: (sträng) namn på den bedömnings profil som används, om något

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

När du har instrumenterat appen och kontrollerat att programmet är korrekt anslutet till Application Insights kan du använda en fördefinierad mall som skapats av Azure Kognitiv sökning för Power BI skriv bord. 

Azure kognitiv search tillhandahåller ett övervaknings [Power BI innehålls paket](https://app.powerbi.com/getdata/services/azure-search) så att du kan analysera loggdata. Innehålls paketet innehåller fördefinierade diagram och tabeller som är användbara för att analysera ytterligare data som registrerats för Sök trafik analys. Mer information finns i den [hjälpsidan för Innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. Klicka på **Sök trafik analys**under **inställningar**i Azure kognitiv sökning instrument panelens vänstra navigerings fönster.

2. På sidan **Sök trafik analys** går du till steg 3 och klickar på **Hämta Power BI Desktop** för att installera Power BI.

   ![Hämta Power BI rapporter](./media/search-traffic-analytics/get-use-power-bi.png "Hämta Power BI rapporter")

2. På samma sida klickar du på **hämta Power BI rapporten**.

3. Rapporten öppnas i Power BI Desktop och du uppmanas att ansluta till Application Insights. Du hittar den här informationen på Azure Portal sidor för din Application Insights-resurs.

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

![Power BI instrument panel för Azure Kognitiv sökning](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI instrument panel för Azure Kognitiv sökning")

## <a name="next-steps"></a>Nästa steg
Instrumentera ditt sökprogram för att få kraftfull och insiktad information om din Sök tjänst.

Du hittar mer information om [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) och besöker sidan med [priser](https://azure.microsoft.com/pricing/details/application-insights/) för att lära dig mer om deras olika tjänst nivåer.

Lär dig mer om att skapa fantastiska rapporter. Mer information finns i [komma igång med Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) .

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
