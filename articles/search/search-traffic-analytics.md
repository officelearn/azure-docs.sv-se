---
title: Telemetri för söktrafikanalys
titleSuffix: Azure Cognitive Search
description: Aktivera söktrafikanalys för Azure Cognitive Search, samla in telemetri och användarinitierade händelser med hjälp av Application Insights och analysera sedan resultaten i en Power BI-rapport.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258217"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Samla in telemetridata för söktrafikanalys

Söktrafikanalys är ett mönster för att samla in telemetri om användarinteraktioner med ditt Azure Cognitive Search-program, till exempel användarinitierade klickhändelser och tangentbordsindata. Med den här informationen kan du bestämma hur effektiv söklösningen är, inklusive populära söktermer, klickfrekvens och vilka frågeindata som ger noll resultat.

Det här mönstret kräver ett beroende av [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (en funktion i [Azure Monitor)](https://docs.microsoft.com/azure/azure-monitor/)för att samla in användardata. Det kräver att du lägger till instrumentering i klientkoden, enligt beskrivningen i den här artikeln. Slutligen behöver du en rapporteringsmekanism för att analysera data. Vi rekommenderar Power BI men du kan använda programinstrumentpanelen eller något verktyg som ansluter till Application Insights.

> [!NOTE]
> Mönstret som beskrivs i den här artikeln är för avancerade scenarier och klickströmsdata som genereras av kod som du lägger till i klienten. Däremot är tjänstloggar enkla att ställa in, tillhandahålla en rad mått och kan göras i portalen utan att någon kod krävs. Aktivera diagnostikloggning rekommenderas för alla scenarier. Mer information finns i [Samla in och analysera loggdata](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identifiera relevanta sökdata

Om du vill ha användbara mått för söktrafikanalys är det nödvändigt att logga vissa signaler från användarna av sökprogrammet. Dessa signaler betyder innehåll som användarna är intresserade av och som de anser vara relevant. För söktrafikanalyser inkluderar dessa:

+ Användargenererade sökhändelser: Endast sökfrågor som initierats av en användare är intressanta. Sökförfrågningar som används för att fylla aspekter, ytterligare innehåll eller intern information, är inte viktiga och de skeva och fördomar dina resultat.

+ Användargenererade klickhändelser: På en sökresultatsida innebär en klickhändelse i allmänhet att ett dokument är ett relevant resultat för en viss sökfråga.

Genom att länka sök- och klickhändelser med ett korrelations-ID får du en djupare förståelse för hur bra programmets sökfunktioner fungerar.

## <a name="add-search-traffic-analytics"></a>Lägga till analys av söktrafik

På [portalsidan](https://portal.azure.com) för din Azure Cognitive Search-tjänst innehåller sidan Söktrafikanalys en lathund för att följa det här telemetrimönstret. På den här sidan kan du välja eller skapa en Application Insights-resurs, hämta instrumenteringsnyckeln, kopiera utdrag som du kan anpassa för din lösning och hämta en Power BI-rapport som är byggd över schemat som återspeglas i mönstret.

![Sidan Sök i Traffic Analytics i portalen](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Sidan Sök i Traffic Analytics i portalen")

## <a name="1---set-up-application-insights"></a>1 - Ställ in programinsikter

Välj en befintlig Application Insights-resurs eller [skapa en](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) om du inte redan har någon. Om du använder sidan Söktrafikanalys kan du kopiera instrumenteringsnyckeln som ditt program behöver för att ansluta till Application Insights.

När du har en Application Insights-resurs kan du följa [instruktionerna för språk och plattformar som stöds för](https://docs.microsoft.com/azure/azure-monitor/app/platforms) att registrera din app. Registrering är helt enkelt att lägga till instrumenteringsnyckeln från Application Insights till din kod, som ställer in associationen. Du hittar nyckeln i portalen eller på sidan Söktrafikanalys när du väljer en befintlig resurs.

En genväg som fungerar för vissa Visual Studio-projekttyper återspeglas i följande steg. Det skapar en resurs och registrerar din app med bara några klick.

1. För utveckling av Visual Studio och ASP.NET öppnar du lösningen och väljer > **Projekttilläggsstatistik Telemetri**. **Project**

1. Klicka på **Kom igång**.

1. Registrera din app genom att tillhandahålla ett Microsoft-konto, En Azure-prenumeration och en Application Insights-resurs (en ny resurs är standard). Klicka på **Registrera**.

Nu är ditt program inställt för programövervakning, vilket innebär att alla sidbelastningar spåras med standardmått. Mer information om föregående steg finns i [Aktivera telemetri på application insights på serversidan](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Lägg till instrumentering

Det här steget är där du instrument ditt eget sökprogram, med hjälp av Application Insights resurs som du skapade i steget ovan. Det finns fyra steg till den här processen, som börjar med att skapa en telemetriklient.

### <a name="step-1-create-a-telemetry-client"></a>Steg 1: Skapa en telemetriklient

Skapa ett objekt som skickar händelser till Application Insights. Du kan lägga till instrumentering i din programkod på serversidan eller kod på klientsidan som körs i en webbläsare, uttryckta här som C#- och JavaScript-varianter (för andra språk finns i den fullständiga listan över [plattformar och ramverk som stöds](https://docs.microsoft.com/azure/application-insights/app-insights-platforms). Välj den metod som ger dig önskat djup av information.

Telemetri på serversidan samlar in mått på programlagret, till exempel i program som körs som en webbtjänst i molnet eller som en lokal app i ett företagsnätverk. Telemetri på serversidan samlar in sök- och klickhändelser, ett dokuments position i resultat och frågeinformation, men datainsamlingen begränsas till all information som finns tillgänglig i det lagret.

På klienten kan du ha ytterligare kod som manipulerar frågeindata, lägger till navigering eller innehåller kontext (till exempel frågor som initierats från en startsida jämfört med en produktsida). Om detta beskriver din lösning kan du välja instrumentering på klientsidan så att telemetrin återspeglar ytterligare information. Hur den här ytterligare detaljen samlas in går utöver det här mönstrets omfattning, men du kan granska [Programinsikter för webbsidor](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) för mer riktning. 

**Använd C #**

För C#finns **InstrumentationKey** i programkonfigurationen, till exempel appsettings.json om projektet ASP.NET. Gå tillbaka till registreringsinstruktionerna om du är osäker på nyckelplatsen.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**Använda JavaScript**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Steg 2: Begär ett sök-ID för korrelation

Om du vill korrelera sökbegäranden med klick måste du ha ett korrelations-ID som relaterar dessa två distinkta händelser. Azure Cognitive Search ger dig ett sök-ID när du begär det med ett HTTP-huvud.

Med sök-ID tillåter korrelation av de mått som avges av Azure Cognitive Search för själva begäran, med anpassade mått som du loggar in Application Insights.  

**Använd C #**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**Använda JavaScript (anropa REST-API:er)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Steg 3: Logga sökhändelser

Varje gång en sökbegäran utfärdas av en användare bör du logga den som en sökhändelse med följande schema på en anpassad application insights-händelse. Kom ihåg att logga endast användargenererade sökfrågor.

+ **Söktjänstnamn**: (sträng) söktjänstnamn
+ **SearchId**: (guid) unik identifierare för sökfrågan (kommer i söksvaret)
+ **IndexName**: (sträng) söktjänstindex som ska efterfrågas
+ **QueryTerms**: (sträng) söktermer som anges av användaren
+ **ResultatCount:**(int) antal dokument som returnerades (kommer i söksvaret)
+ **ScoringProfile**: (sträng) namn på den poängprofil som används, om någon

> [!NOTE]
> Begär antalet användargenererade frågor genom att lägga till $count=true i sökfrågan. Mer information finns i [Sökdokument (REST)](/rest/api/searchservice/search-documents#counttrue--false).
>

**Använd C #**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**Använda JavaScript**

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Steg 4: Logga klicka händelser

Varje gång en användare klickar på ett dokument är det en signal som måste loggas för sökanalys. Använd anpassade händelser i Application Insights för att logga dessa händelser med följande schema:

+ **ServiceName**: (sträng) söktjänstnamn
+ **SearchId**: (guid) unik identifierare för den relaterade sökfrågan
+ **DocId**: (sträng) dokumentidentifierare
+ **Position**: (int) rangordning av dokumentet i sökresultatsidan

> [!NOTE]
> Position refererar till kardinalordningen i din ansökan. Du är fri att ställa in detta nummer, så länge det alltid är samma, för att möjliggöra jämförelse.
>

**Använd C #**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**Använda JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Analysera i Power BI

När du har instrumenterat din app och verifierat att ditt program är korrekt anslutet till Application Insights hämtar du en fördefinierad rapportmall för att analysera data på Power BI-skrivbordet. Rapporten innehåller fördefinierade diagram och tabeller som är användbara för att analysera ytterligare data som hämtas för söktrafikanalys.

1. Klicka på **Sök trafikanalys**under **Inställningar**i instrumentpanelen för Azure Cognitive Search till vänster navigering.

1. Klicka på **Hämta Power BI Desktop** i steg 3 på sidan Sök **trafikanalys** för att installera Power BI.

   ![Hämta Power BI-rapporter](./media/search-traffic-analytics/get-use-power-bi.png "Hämta Power BI-rapporter")

1. Klicka på Hämta **Power BI-rapport**på samma sida.

1. Rapporten öppnas i Power BI Desktop och du uppmanas att ansluta till Application Insights och ange autentiseringsuppgifter. Du kan hitta anslutningsinformation på Azure-portalsidorna för din Application Insights-resurs. För autentiseringsuppgifter anger du samma användarnamn och lösenord som du använder för portalloggning.

   ![Anslut till Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Anslut till Application Insights")

1. Klicka på **Läs in**.

Rapporten innehåller diagram och tabeller som hjälper dig att fatta mer välgrundade beslut för att förbättra dina sökresultat och din relevans.

Mått inkluderade följande objekt:

+ Sök volym och mest populära term-dokument par: termer som resulterar i samma dokument klickade, ordnade med klick.
+ Söker utan klick: termer för toppfrågor som inte registrerar några klick

Följande skärmbild visar hur en inbyggd rapport kan se ut om du har använt alla schemaelement.

![Power BI-instrumentpanel för Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI-instrumentpanel för Azure Cognitive Search")

## <a name="next-steps"></a>Nästa steg

Instrumentera ditt sökprogram för att få kraftfulla och insiktsfulla data om din söktjänst.

Du hittar mer information om [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) och besöker [prissidan](https://azure.microsoft.com/pricing/details/application-insights/) om du vill veta mer om deras olika tjänstnivåer.

Läs mer om hur du skapar fantastiska rapporter. Mer information [finns i Komma igång med Power BI Desktop.](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)