---
title: En rundtur bland Analytics i Azure Application Insights | Microsoft Docs
description: Kort exempel av de viktigaste frågorna i Analytics, verktyget kraftfull sökning av Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: mbullwin
ms.openlocfilehash: 470779f80e998c3908cf28328cfb415d98c5e06c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579263"
---
# <a name="a-tour-of-analytics-in-application-insights"></a>En genomgång av Analytics i Application Insights
[Analytics](app-insights-analytics.md) är en kraftfull sökning funktion till [Application Insights](app-insights-overview.md). Dessa sidor beskrivs Log Analytics-frågespråket.

* **[Titta på introduktionsvideon](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testkör Analytics på våra simulerade data](https://analytics.applicationinsights.io/demo)**  om din app inte skickar data till Application Insights ännu.
* **[SQL-användarnas Lathund](https://aka.ms/sql-analytics)**  översätter vanligaste idioms.

Låt oss ta en genomgång av några grundläggande frågor att komma igång.

## <a name="connect-to-your-application-insights-data"></a>Anslut till Application Insights-data
Öppna Analytics från din app [översiktsbladet](app-insights-dashboards.md) i Application Insights:

![Öppna portal.azure.com, öppna Application Insights-resursen och klicka på Analytics.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsiodocslanguage-referencetabular-operators-show-me-n-rows"></a>[Ta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators): Visa mig n rader
Datapunkter som loggar användaråtgärder (vanligtvis HTTP-begäranden som tas emot av din webbapp) lagras i en tabell med namnet `requests`. Varje rad är en datapunkt för telemetri som togs emot från Application Insights SDK i din app.

Låt oss börja genom att undersöka några exempel raderna i tabellen:

![resultat](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Placera markören någonstans i instruktionen innan du klickar på OK. Du kan dela upp en instruktion på mer än en rad, men Placera inte tomma rader i en instruktion. Tomma rader är ett praktiskt sätt att hålla flera separata frågor i fönstret.
>
>

Välj kolumner, dra dem, gruppera efter kolumner, och filtrera:

![Klicka på Kolumnurval i övre högra hörnet av resultat](./media/app-insights-analytics-tour/030.png)

Expandera alla objekt om du vill se detaljerad information:

![Välj tabell och använda Konfigurera kolumner](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Klicka på rubriken för en kolumn att ordna om resultatet i webbläsaren. Men tänk på att antalet rader som hämtas till webbläsaren för en stor resultatuppsättning är begränsad. Sortera vis bara sorterar den returnerade resultatuppsättningen och alltid visar inte de faktiska högsta eller lägsta artiklarna. Om du vill sortera objekt på ett tillförlitligt sätt att använda den `top` eller `sort` operator.
>
>

## <a name="query-across-applications"></a>Fråga mellan program
Om du vill kombinera data från flera Application Insights-program kan använda den **app** nyckelord att ange program tillsammans med tabellnamnet.  Den här frågan kombinerar förfrågningar från två olika program med hjälp av den **union** kommando.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsiodocslanguage-referencetabular-operatorstop-operator-and-sorthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssort-operator"></a>[Översta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) och [sortera](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
`take` är användbar för att få ett snabbt exempel på ett resultat, men den visar rader från tabellen i någon särskild ordning. Hämta en ordnad vy med `top` (till exempel) eller `sort` (över hela tabellen).

Visa de första n raderna, sorterade efter en viss kolumn:

```AIQL

    requests | top 10 by timestamp desc
```

* *Syntax:* de flesta operatörer har nyckelordet parametrar som `by`.
* `desc` = fallande ordning `asc` = stigande.

![](./media/app-insights-analytics-tour/260.png)

`top...` är ett mer effektivt sätt att saying `sort ... | take...`. Vi kan ha skrivit:

```AIQL

    requests | sort by timestamp desc | take 10
```

Resultatet är samma, men körs det lite längre tid. (Du kan också skriva `order`, vilket är ett alias för `sort`.)

Kolumnrubrikerna i tabellvyn kan också användas för att sortera resultaten på skärmen. Men om du har använt självklart, `take` eller `top` för att hämta bara en del av en tabell, som att klicka på kolumnrubriken kommer endast ordna om de poster som du har hämtat.

## <a name="wherehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorswhere-operator-filtering-on-a-condition"></a>[Där](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator): filtrering på ett villkor

Nu ska vi se bara begäranden som returnerade en viss Resultatkod:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

Den `where` operator tar ett booleskt uttryck. Här följer några viktiga saker om dem:

* `and`, `or`: Booleska operatorer
* `==`, `<>`, `!=` : är lika med och inte lika med
* `=~`, `!~` : Skiftlägesokänslig sträng lika och inte lika med. Det finns många fler sträng-jämförelseoperatorer.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Sök efter misslyckade förfrågningar

Konvertera ett strängvärde till ett heltal och använda större-än jämförelse:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Tid

Som standard är frågorna begränsade till den senaste 24 timmarna. Men du kan ändra detta intervall:

![](./media/app-insights-analytics-tour/change-time-range.png)

Åsidosätt tidsintervallet genom att skriva en fråga om `timestamp` i en where-sats. Exempel:

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

Funktionen tid intervallet motsvarar en ”where”-sats infogas efter varje uppgift om någon av källtabellerna.

`ago(3d)` innebär att ”tre dagar sedan”. Andra tidsenheter vara arbetstider (`2h`, `2.5h`), minuter (`25m`), och sekunder (`10s`).

Andra exempel:

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

    // Between specific day/time range
    requests
    | where timestamp > datetime(2018-05-17T17:06:19.892Z) and timestamp <= datetime(2018-05-18T17:06:19.892Z)
    | where duration > 0

```

[Datum och tider referens](https://docs.loganalytics.io/docs/Language-Reference/Data-types/datetime).


## <a name="projecthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorsproject-operator-select-rename-and-compute-columns"></a>[Projektet](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator): välja, byta namn på och compute kolumner
Använd [ `project` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator) att välja ut bara de kolumner som du vill:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Du kan också byta namn på kolumner och definiera nya:

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![Resultatet](./media/app-insights-analytics-tour/270.png)

* Kolumnnamn kan innehålla blanksteg eller symboler om de avslutas så här: `['...']` eller `["..."]`
* `%` är vanligt modulo operator.
* `1d` (som är en siffra, en hade ”) är en literal timespan vilket innebär att en dag. Här följer några fler timespan-litteraler: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) Avrundar ett värde nedåt till närmaste multipel för basvärdet som du anger. Så `floor(aTime, 1s)` Avrundar taget nedåt till närmaste sekund.

Uttryck kan innehålla alla vanliga operatorer (`+`, `-`,...), och det finns ett antal användbara funktioner.

## <a name="extend"></a>Utöka
Om du vill lägga till kolumner i befintliga använda [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Med hjälp av [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator) är mindre utförlig än [ `project` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator) om du vill behålla alla befintliga kolumner.

### <a name="convert-to-local-time"></a>Konvertera till lokal tid

Tidsstämplar är alltid i UTC. Så om du är på oss Pacific havet och det är vinter, är den lokala tiden-8 timmar från UTC, kan du så här:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```

## <a name="summarizehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssummarize-operator-aggregate-groups-of-rows"></a>[Sammanfatta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator): aggregera grupper av rader
`Summarize` gäller en angiven *aggregeringsfunktionen* över grupper av rader.

Till exempel den tid din webbapp tar för att svara på en begäran rapporteras i fältet `duration`. Nu ska vi se den genomsnittliga svarstiden för alla begäranden:

![](./media/app-insights-analytics-tour/410.png)

Eller vi kunde dela resultatet i begäranden med olika namn:

![](./media/app-insights-analytics-tour/420.png)

`Summarize` samlar in datapunkter i strömmen i grupper som den `by` satsen utvärderar lika. Varje värde i den `by` uttrycket - varje unika åtgärds-namnet i exemplet ovan - resulterar i en rad i resultattabellen.

Eller vi kan gruppera resultatet efter tid på dagen:

![](./media/app-insights-analytics-tour/430.png)

Observera hur vi använder den `bin` funktion (även kallat `floor`). Om du bara använder `by timestamp`, var indata rad skulle hamnar i en egen liten grupp. För alla kontinuerlig skalära som gånger eller siffror, måste vi dela upp kontinuerlig intervallet i flera hanterbara med diskreta värden. `bin` – vilket är bara det välbekanta avrundning nedåt `floor` fungera – är det enklaste sättet att göra detta.

Vi kan använda samma metod för att minska intervallen för strängar:

![](./media/app-insights-analytics-tour/440.png)

Observera att du kan använda `name=` att ange namnet på en resultatkolumn, antingen i mängduttryck eller av-satsen.

## <a name="counting-sampled-data"></a>Räkna exempeldata
`sum(itemCount)` är den rekommenderade aggregeringen till antal händelser. I många fall itemCount == 1, så funktionen räknar helt enkelt upp antalet rader i gruppen. Men när [sampling](app-insights-sampling.md) är i drift, bara en bråkdel av de ursprungliga händelserna behålls som datapunkter i Application Insights, så att det finns för varje datapunkt som du ser, `itemCount` händelser.

Till exempel om sampling ignorerar 75% av den ursprungliga händelser och sedan itemCount == 4 i sparade poster – det vill säga i varje sparade post, det fanns fyra ursprungliga poster.

Adaptiv sampling orsakar itemCount till under perioder när ditt program som ofta används.

Addera itemCount därför ger en bra uppskattning av det ursprungliga antalet händelser.

![](./media/app-insights-analytics-tour/510.png)

Det finns också en `count()` sammansättning och ett antal åtgärden, i de fall där du verkligen vill räkna antalet rader i en grupp.

Det finns en mängd [aggregeringsfunktioner](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions).

## <a name="charting-the-results"></a>Diagram resultaten
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Som standard visar resultat som en tabell:

![](./media/app-insights-analytics-tour/225.png)

Vi kan göra bättre än tabellvyn. Nu ska vi titta på resultatet i schemat med lodrätt stapel alternativet:

![Klicka på diagrammet, Välj stående stapeldiagram och tilldela sedan x- och y axlar](./media/app-insights-analytics-tour/230.png)

Observera att även om vi inte sortera resultaten efter tid (som du ser i tabellen visas), hur diagram visar alltid datum och tid i rätt ordning.


## <a name="timecharts"></a>Timecharts
Visa det hur många händelser är varje timme:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Välj diagrammet Visningsalternativ:

![tidsdiagram](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Flera serier
Flera uttryck i den `summarize` satsen skapar flera kolumner.

Flera uttryck i den `by` satsen skapar flera rader, en för varje kombination av värden.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tabell med förfrågningar per timme och plats](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Segmentera ett diagram av dimensioner
Strängen kan användas för att dela upp numeriska data i separata mängd punkter om du skapa diagram över en tabell som har en strängkolumn och en numerisk kolumn. Om det finns fler än en strängkolumn, kan du välja vilken kolumn som ska användas som diskriminator.

![Segmentera en analytics-diagram](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Studshastighet

Konvertera ett booleskt värde till en sträng som ska använda den som diskriminator:

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>Visa flera mått
Om du skapa diagram över en tabell som har mer än en numerisk kolumn, förutom tidsstämpel, kan du visa en kombination av dessa.

![Segmentera en analytics-diagram](./media/app-insights-analytics-tour/110.png)

Du måste välja **inte dela** innan du kan välja flera numeriska kolumner. Du kan inte dela av en strängkolumn samtidigt som visar mer än en numerisk kolumn.

## <a name="daily-average-cycle"></a>Daglig genomsnittlig cykel
Hur användningen variera över genomsnittlig dag?

Antal begäranden med tiden modulo en dag binned till timmar:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Linjediagram med timmar i en genomsnittlig dag](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Observera att vi har för närvarande att konvertera tidsvaraktigheter till datum och tid för att visa på ett linjediagram.
>
>

## <a name="compare-multiple-daily-series"></a>Jämföra flera dagliga serier
Hur användningen variera över tid på dagen i olika länder?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![Delning av client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Rita en distribution
Hur många sessioner finns det med olika längd?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

Den sista raden krävs för att konvertera till datetime. För närvarande visas x-axeln i ett diagram som en skalär endast om det är ett datetime-värde.

Den `where` satsen utesluter one-shot sessioner (sessionDuration == 0) och anger längden på x-axeln.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsiodocslanguage-referenceaggregation-functionspercentiles"></a>[Percentiler](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/percentiles())
Vilka intervallen med varaktigheter täcka olika procentandelar av sessioner?

Använd ovanstående fråga, men ersätt den sista raden:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Vi också har tagit bort den övre gränsen i where-satsen för att få rätt uppgifter som inkluderar alla sessioner med mer än en begäran:

![Resultatet](./media/app-insights-analytics-tour/180.png)

Där ser vi att:

* 5% av sessioner har en varaktighet på mindre än 3 minuter 34s;
* 50% av sessioner senaste mindre än 36 minuter.
* 5% av sessioner senaste mer än 7 dagar

Om du vill ha en separat analys på detaljnivå för varje land/region, vi behöver flytta sammanfatta kolumnen client_CountryOrRegion separat via båda operatorer:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Slå ihop
Vi har åtkomst till flera tabeller, inklusive begäranden och undantag.

För att hitta de undantag som rör en begäran som returnerade ett felsvar, kan vi ansluta till tabellerna på `operation_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


Det är bra att använda `project` att välja bara de kolumner vi behöver innan du utför i kopplingen.
I samma-satser vi byta namn på kolumn för tidsstämpel.

## <a name="lethttpsdocsloganalyticsiodocslanguage-referencequery-statementslet-statement-assign-a-result-to-a-variable"></a>[Låt](https://docs.loganalytics.io/docs/Language-Reference/Query-statements/Let-statement): tilldela ett resultat till en variabel

Använd `let` att skilja ut delarna av det föregående uttrycket. Resultatet är oförändrat:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> I Analytics-klient Placera inte tomma rader mellan delar av frågan. Se till att köra dem.
>

Använd `toscalar` att konvertera en tabellcell till ett värde:

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Functions

Använd *låta* att definiera en funktion:

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Åtkomst till kapslade objekt
Kapslade objekt kan nås enkelt. Du kan till exempel se strukturerade objekt så här i strömmen undantag:

![Resultatet](./media/app-insights-analytics-tour/520.png)

Du kan förenkla den genom att välja de egenskaper som du är intresserad av:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Observera att du måste konvertera resultatet till lämplig typ.


## <a name="custom-properties-and-measurements"></a>Anpassade egenskaper och mätningar
Om programmet ansluts [anpassade dimensioner (Egenskaper) och anpassade mått](app-insights-api-custom-events-metrics.md#properties) på händelser, sedan du ser dem i den `customDimensions` och `customMeasurements` objekt.

Exempel: om din app innehåller:

```csharp

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2.d2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Att extrahera dessa värden i Analytics:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type
```

För att verifiera om en anpassad dimension är av en viss typ:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

### <a name="special-characters"></a>Specialtecken

För identifierare med specialtecken eller nyckelord i sina namn, måste du komma åt dem via `['` och `']` eller med hjälp av `["` och `"]`.

```AIQL

    customEvents
    | extend p2d2 = customDimensions.['p2.d2'], ...
```

[Referera till regler för namngivning av identifierare](https://docs.loganalytics.io/docs/Learn/References/Naming-principles)

## <a name="dashboards"></a>Instrumentpaneler
Du kan fästa dina resultat på en instrumentpanel för att samla alla dina viktigaste diagram och tabeller.

* [Azure delade instrumentpanelen](app-insights-dashboards.md#share-dashboards): Klicka på ikonen PIN-kod. Innan du gör detta måste du ha en delad instrumentpanel. Öppna Azure portal, eller skapa en instrumentpanel och klicka på resursen.
* [Power BI-instrumentpanel](app-insights-export-power-bi.md): Klicka på Exportera, Power BI-fråga. En fördel med detta alternativ är att du kan visa din fråga tillsammans med andra resultat från en mängd olika källor.

## <a name="combine-with-imported-data"></a>Kombinera med importerade data

Analysrapporter se bra ut på instrumentpanelen, men ibland vill du kunna konvertera data i ett datamängder formulär. Anta exempelvis att din autentiserade användare identifieras i telemetrin av ett alias. Du vill visa sitt riktiga namn i dina resultat. Om du vill göra detta måste behöver du en CSV-fil som mappar från alias till de riktiga namn.

Du kan importera en datafil och använda det precis som med någon av standardtabeller (begäranden, undantag och så vidare). Antingen frågar den på egen hand eller ansluta den till andra tabeller. Exempel: Om du har en tabell med namnet usermap och den har kolumner `realName` och `userId`, och sedan använda den för att översätta det `user_AuthenticatedId` i begärandetelemetri:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Importera en tabell i bladet schemat under **andra datakällor**, följ instruktionerna för att lägga till en ny datakälla genom att ladda upp ett urval av dina data. Du kan sedan använda den här definitionen för att ladda upp tabeller.

Importera funktionen är för närvarande i förhandsversion, så visas först en länk för ”Kontakta oss” under ”andra datakällor”. Används för att registrera dig för programmet för förhandsversionen och länken kommer sedan att ersättas av en ”Lägg till ny datakälla”-knappen.


## <a name="tables"></a>Tabeller
Ström av telemetri som togs emot från din app är tillgänglig via flera tabeller. Schemat för egenskaper som är tillgängliga för varje tabell som visas längst till vänster i fönstret.

### <a name="requests-table"></a>Tabellen ”Requests”
Antal HTTP-begäranden till din webbapp och segment per sidnamn:

![Antal begäranden uppdelat efter namn](./media/app-insights-analytics-tour/analytics-count-requests.png)

Sök efter önskade begäranden som inte uppfyller de flesta:

![Antal begäranden uppdelat efter namn](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Anpassade händelser tabell
Om du använder [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) för att skicka dina egna händelser, kan du läsa dem från den här tabellen.

Låt oss ta ett exempel där din Appkod innehåller följande rader:

```csharp

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Visa hur ofta dessa händelser:

![Visa frekvensen för anpassade händelser](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Extrahera mått och dimensioner från händelser:

![Visa frekvensen för anpassade händelser](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Anpassade mått tabell
Om du använder [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) för att skicka dina egna mått värden, hittar du resultaten i den **customMetrics** stream. Exempel:  

![Anpassade mått i Application Insights analytics](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> I [Måttutforskaren](app-insights-metrics-explorer.md), alla anpassade mått som är kopplade till någon typ av telemetri visas tillsammans i måttbladet tillsammans med mått som skickas med `TrackMetric()`. Men i Analytics, anpassade mått fortfarande kopplad till typ av telemetri som de har gjort på - händelser eller begäranden och så vidare – medan mätvärden som skickas av TrackMetric visas i sina egna stream.
>
>

### <a name="performance-counters-table"></a>Prestandatabell räknare
[Prestandaräknare](app-insights-performance-counters.md) visar grundläggande systemmått för din app, till exempel processor, minne och nätverksanvändning. Du kan konfigurera SDK för att skicka ytterligare räknare, inklusive dina egna anpassade prestandaräknare.

Den **performanceCounters** schemat exponerar den `category`, `counter` namn, och `instance` för varje prestandaräknare. Namn på prestandaräknare instansen gäller endast för vissa prestandaräknare och anger namnet på processen som antalet avser vanligtvis. I telemetri för varje program visas endast räknarna för programmet. Till exempel om du vill se vilka räknare är tillgängliga:

![Prestandaräknare i Application Insights analytics](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Hämta ett diagram av det tillgängliga minnet under den valda perioden:

![Minne tidsdiagram i Application Insights analytics](./media/app-insights-analytics-tour/analytics-available-memory.png)

Som andra telemetri **performanceCounters** också har en kolumn `cloud_RoleInstance` värde som anger identiteten för värddatorn som din app körs. Till exempel att jämföra prestanda för din app på olika datorer:

![Prestanda uppdelat efter rollinstans i Application Insights analytics](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Undantag tabell
[Undantag som rapporterats av din app](app-insights-asp-net-exceptions.md) är tillgängliga i den här tabellen.

Delta i operation_Id för att hitta HTTP-förfrågan som din app hanterades när undantagsfel inträffade:

![Anslut till undantag med begäranden operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Webbläsaren tidsinställningar tabell
`browserTimings` Visar data om webbsidesinläsning som samlas in i användarnas webbläsare.

[Konfigurera din app för klientsidan telemetri](app-insights-javascript.md) om du vill se de här måtten.

Schemat innehåller [mått som anger längden på olika faser av sidan processen för inläsning](app-insights-javascript.md#page-load-performance). (De inte anger hur lång tid som dina användare läsa en sida.)  

Visa popularities på olika sidor och läsa in tiderna för varje sida:

![Sidinläsningstider i Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>Tabellen för tillgänglighet resultat
`availabilityResults` Visar resultatet av dina [webbtester](app-insights-monitor-web-app-availability.md). Varje körning av dina tester från varje testplats rapporteras separat.

![Sidinläsningstider i Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Beroenden tabell
Innehåller resultatet av att din app som gör att databaser och REST API: er och andra anrop till TrackDependency()-anrop. Innehåller också AJAX-anrop som görs från webbläsaren.

AJAX-anrop i webbläsaren:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Beroendeanrop från servern:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Beroende på serversidan resultatet visar alltid `success==False` om Application Insights-agenten inte är installerad. Andra data är dock korrekt.

### <a name="traces-table"></a>Spårningar tabell
Innehåller telemetri som skickas av din app med TrackTrace(), eller [andra loggningsramverk](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Video 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Avancerade frågor:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>Nästa steg
* [Språkreferens för analys](app-insights-analytics-reference.md)
* [SQL-användarnas Lathund](https://aka.ms/sql-analytics) översätter vanligaste idioms.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
