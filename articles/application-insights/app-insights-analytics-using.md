---
title: Använda Analytics - verktyget kraftfull sökning av Azure Application Insights | Microsoft Docs
description: 'Med Analytics, kraftfulla diagnostik sökverktyget för Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2018
ms.reviewer: danha
ms.author: mbullwin
ms.openlocfilehash: aa86e2f3b1fb147ab167c948475a5207693143c2
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341570"
---
# <a name="using-analytics-in-application-insights"></a>Med hjälp av analys i Application Insights
[Analytics](app-insights-analytics.md) är en kraftfull sökning funktion till [Application Insights](app-insights-overview.md). Dessa sidor beskrivs Log Analytics-frågespråket.

* **[Titta på introduktionsvideon](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testkör Analytics på våra simulerade data](https://analytics.applicationinsights.io/demo)**  om din app inte skickar data till Application Insights ännu.

## <a name="open-analytics"></a>Öppna Analytics
Från appens home-resursen i Application Insights, klickar du på Analytics.

![Öppna portal.azure.com, öppna Application Insights-resursen och klicka på Analytics.](./media/app-insights-analytics-using/001.png)

Infogad självstudien får du några idéer om vad du kan göra.

Det finns en [mer omfattande genomgång här](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Fråga din telemetri
### <a name="write-a-query"></a>Skriv en fråga
![Visa schemat](./media/app-insights-analytics-using/150.png)

Börja med namnen på någon av de tabeller som visas till vänster (eller [intervallet](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) eller [union](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator) operatörer). Använd `|` att skapa en pipeline för [operatörer](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

IntelliSense efterfrågar med operatorerna och Uttryckselement som du kan använda. Klicka på informationsikonen (eller tryck på CTRL + blanksteg) att hämta en utförligare beskrivning och exempel på hur du använder varje element.

Se den [Analytics språk rundtur](app-insights-analytics-tour.md) och [Språkreferens](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Köra en fråga
![En fråga som körs](./media/app-insights-analytics-using/130.png)

1. Du kan använda enkel radbrytningar i en fråga.
2. Placera markören i eller i slutet av den fråga som du vill köra.
3. Kontrollera tidsintervallet på din fråga. (Du kan ändra den, eller Åsidosätt den genom att inkludera en egen [ `where...timestamp...` ](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) -sats i frågan.)
3. Klicka på OK om du vill köra frågan.
4. Placera inte tomma rader i frågan. Du kan behålla flera avgränsas frågor i fliken för en fråga genom att avgränsa dem med tomma rader. Endast den fråga som innehåller markören körs.

### <a name="save-a-query"></a>Spara en fråga
![Spara en fråga](./media/app-insights-analytics-using/140.png)

1. Spara den aktuella fråga-filen.
2. Öppna en sparad fråga-fil.
3. Skapa en ny fil i fråga.

## <a name="see-the-details"></a>Visa detaljerad information
Expandera alla rader i resultat för att se dess heltäckande lista med egenskaper. Du kan ytterligare Expandera egenskaper som är ett strukturerat värde – till exempel, anpassade dimensioner eller stack i ett undantag.

![Expandera en rad](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Ordna resultaten
Du kan sortera, filtrera, sidbryta och gruppera resultat som returneras från frågan.

> [!NOTE]
> Sortering, gruppering och filtrering i webbläsaren kör inte frågan igen. De endast ordna resultaten som returnerades av din senaste fråga. 
> 
> Om du vill utföra dessa funktioner på servern innan resultaten returneras, skriva en fråga med den [sortera](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [sammanfatta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) och [där](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) operatörer.
> 
> 

Välj de kolumner som du skulle vilja se, dra kolumnrubrikerna Om du vill ordna om dem och ändra storlek på kolumner genom att dra deras gränser.

![Ordna kolumner](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Sortera och filtrera objekt
Sortera resultaten genom att klicka i sidhuvudet för en kolumn. Klickar du på nytt om du vill sortera det andra sättet klicka sedan på tredje tid att återgå till den ursprungliga ordningen som returneras av frågan.

Använd filter-ikonen för att begränsa sökningen.

![Kolumnerna för sortering och filter](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Gruppera objekt
Sortera efter mer än en kolumn genom att använda gruppering. Först aktivera det och dra sedan kolumnrubriker i området ovanför tabellen.

![Grupp](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Saknar vissa resultat?

Om du tror att du inte kan se alla resultat som du förväntade dig, finns det några möjliga orsaker.

* **Intervallet tidsfiltret**. Som standard visas endast resultat från de senaste 24 timmarna. Det finns en automatisk filter som begränsar resultaten som hämtas från källtabellerna intervallet. 

    Du kan dock ändra tidsintervallet filter från den nedrullningsbara menyn.

    Eller du kan åsidosätta det automatiska intervallet genom att inkludera en egen [ `where  ... timestamp ...` satsen](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) i din fråga. Exempel:

    `requests | where timestamp > ago('2d')`

* **Resultaten gränsen**. Det finns en gräns på cirka 10 k rader på de resultat som returnerats från portalen. En varning visas om du överskrider gränsen. Om detta händer, visar sortera resultaten i tabellen inte alltid alla faktiska första eller sista resultat. 

    Det är bra att undvika nått gränsen. Använda tidsfiltret för intervallet, eller Använd operatorer som:

  * [de 100 främsta efter tidsstämpel](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [ta 100](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [Sammanfatta ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [där timestamp > ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(Du vill att fler än 10 k rader? Överväg att använda [löpande Export](app-insights-export-telemetry.md) i stället. Analytics är avsett för analys, i stället för att hämta rådata.)

## <a name="diagrams"></a>Diagram
Välj typ av diagram som du vill ha:

![Välj en diagramtyp](./media/app-insights-analytics-using/230.png)

Om du har flera kolumner med rätt typer kan välja du x och y-axlarna och en kolumn med dimensioner att dela resultatet av.

Som standard visas först som en tabell och du väljer diagrammet manuellt. Men du kan använda den [rendera direktiv](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) i slutet av en fråga för att välja ett diagram.

### <a name="analytics-diagnostics"></a>Analytics-diagnostik


Om det finns en plötslig insamling eller steg i dina data på ett tidsdiagram kan du se en markerad punkt på raden. Detta anger att Analytics-diagnostik har identifierat en kombination av egenskaper som filtrerar bort plötsliga ändringen. Klicka på punkten att hämta mer information om filtret och för att se den filtrera versionen. Detta kan hjälpa dig att identifiera vad som orsakade ändringen. 

[Mer information om Analytics diagnostik](app-insights-analytics-diagnostics.md)


![Analytics-diagnostik](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Fäst vid instrumentpanelen
Du kan fästa ett diagram eller en tabell till en av dina [delade instrumentpaneler](app-insights-dashboards.md) -Klicka bara på PIN-koden. 

![Klicka på PIN-koden](./media/app-insights-analytics-using/pin-01.png)

Det innebär att, du kan ta ganska komplexa analyser tillsammans med andra mått när du sätter ihop en instrumentpanel för att övervaka prestanda och användning av dina webbtjänster. 

Du kan fästa en tabell till instrumentpanelen, om den har fyra eller färre kolumner. Endast de översta sju raderna visas.

### <a name="dashboard-refresh"></a>Uppdatering av instrumentpanelen
Diagrammet fäst på instrumentpanelen uppdateras automatiskt genom att köra frågan igen ungefär varje timme. Du kan också klicka på Uppdatera-knappen.

### <a name="automatic-simplifications"></a>Automatisk förenklingar

Vissa förenklingar tillämpas på ett diagram när du fäster den på en instrumentpanel.

**Tid begränsning:** frågor begränsas automatiskt till de senaste 30 dagarna. Den har samma effekten som om frågan innehåller `where timestamp > ago(30d)`.

**Bin antal begränsning:** om du visar ett diagram med en massa diskreta lagerplatser (vanligtvis ett stapeldiagram), mindre ifylld lagerplatser automatiskt är grupperade i en enda ”andra” bin. Exempelvis kan den här frågan:

    requests | summarize count_search = count() by client_CountryOrRegion

ser ut så här i Analytics:

![Diagram med lång pilslut](./media/app-insights-analytics-using/pin-07.png)

men när du fäster den på en instrumentpanel, det ser ut så här:

![Diagram med begränsad lagerplatser](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Exportera till Excel
När du har kört en fråga, kan du hämta en CSV-fil. Klicka på **Exportera Excel**.

## <a name="export-to-power-bi"></a>Exportera till Power BI
Placera markören i en fråga och välj **exportera Power BI**.

![Exportera från analys till Powerbi](./media/app-insights-analytics-using/240.png)

Du kör frågan i Power BI. Du kan ange att uppdatera enligt ett schema.

Med Power BI kan skapa du instrumentpaneler som sammanför ihop data från en mängd olika källor.

[Mer information om export till Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Djuplänk

Hämta en länk under **Export, delningslänk** som du kan skicka till en annan användare. Förutsatt att användaren har [åtkomst till resursgruppen](app-insights-resources-roles-access-control.md), frågan öppnas i Användargränssnittet för analys.

(I länken, Frågetexten visas efter ”? q =”, gzip-komprimerat och Base64-kodad. Du kan skriva kod för att generera djuplänkar som du anger för användare. Men det rekommenderade sättet att köra analyser från kod är med hjälp av den [REST API](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automation

Använd den [Data Access REST API: et](https://dev.applicationinsights.io/) att köra analysfrågor. [Till exempel](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (med PowerShell):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

Till skillnad från Gränssnittet för Analytics läggs REST API inte automatiskt alla tidsstämpel-begränsningar i dina frågor. Kom ihåg att lägga till egna where-satsen, om du vill undvika stora svar.



## <a name="import-data"></a>Importera data

Du kan importera data från en CSV-fil. En typisk användning är att importera statiska data som du kan ansluta till med tabeller från din telemetri. 

Till exempel kan autentiserade användare identifieras i din telemetri från ett alias eller dold id, du importera en tabell som mappar alias till riktiga namn. Genom att utföra en koppling på begärandetelemetri kan identifiera du användare efter deras verkliga namn i Analytics-rapporter.

### <a name="define-your-data-schema"></a>Definiera din dataschema

1. Klicka på **inställningar** (längst upp till vänster) och sedan **datakällor**. 
2. Lägg till en datakälla, följa anvisningarna. Du uppmanas att ange ett exempel på data som ska innehålla minst 10 raderna. Korrigera schemat.

Detta definierar en datakälla, där du kan importera enskilda tabeller.

### <a name="import-a-table"></a>Importera en tabell

1. Öppna din definition av datakällan i listan.
2. Klicka på ”ladda upp” och följ instruktionerna för att ladda upp tabellen. Detta innebär att ett anrop till ett REST-API och är enkelt att automatisera. 

Tabellen är nu tillgänglig för användning i Analytics-frågor. Den visas i Analytics 

### <a name="use-the-table"></a>Använd tabell

Låt oss anta definitionen av din datakälla heter `usermap`, och att det finns två fält, `realName` och `user_AuthenticatedId`. Den `requests` tabellen har också ett fält med namnet `user_AuthenticatedId`, så det är enkelt att ansluta till dem:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
Den resulterande tabellen begäranden har ytterligare en kolumn, `realName`.

### <a name="import-from-logstash"></a>Importera från LogStash

Om du använder [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), du kan använda Analytics för att fråga efter dina loggar. Använd den [plugin-program som rör data till Analytics](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

