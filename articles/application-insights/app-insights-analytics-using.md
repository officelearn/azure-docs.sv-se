---
title: "Med hjälp av Analytics - kraftfullt sökverktyg av Azure Application Insights | Microsoft Docs"
description: "Med hjälp av Analytics, kraftfullt diagnostiska sökverktyg av Application Insights. "
services: application-insights
documentationcenter: 
author: danhadari
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: danha; mbullwin
ms.openlocfilehash: 0ca5c8b19f4699548a8551ec673e4a067d4e5fad
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="using-analytics-in-application-insights"></a>Med hjälp av Analytics i Application Insights
[Analytics](app-insights-analytics.md) är kraftfull sökfunktionen i [Programinsikter](app-insights-overview.md). Dessa sidor beskrivs Log Analytics-frågespråket.

* **[Titta på inledande videon](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testkör Analytics på våra simulerade data](https://analytics.applicationinsights.io/demo)**  om din app inte skickar data till Application Insights ännu.

## <a name="open-analytics"></a>Öppna Analytics
Klicka på Analytics från din app hem resurs i Application Insights.

![Öppna portal.azure.com, öppna Application Insights-resursen och klicka på Analytics.](./media/app-insights-analytics-using/001.png)

Infogade kursen får du några förslag på vad du kan göra.

Det finns en [mer omfattande rundtur här](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Fråga din telemetri
### <a name="write-a-query"></a>Skriva en fråga
![Visa schema](./media/app-insights-analytics-using/150.png)

Börja med namnen på någon av de tabeller som visas till vänster (eller [intervallet](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) eller [union](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator) operatörer). Använd `|` du skapar en pipeline för [operatörer](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

IntelliSense får operatörerna och Uttryckselement som du kan använda. Klicka på informationsikonen (eller trycker på CTRL + blanksteg) att hämta en utförligare beskrivning och exempel på hur du använder varje element.

Finns det [Analytics språk rundtur](app-insights-analytics-tour.md) och [Språkreferens](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Köra en fråga
![Kör en fråga](./media/app-insights-analytics-using/130.png)

1. Du kan använda enkel radbrytningar i en fråga.
2. Placera markören i eller i slutet av du vill köra frågan.
3. Kontrollera tidsintervallet på din fråga. (Du kan ändra det eller åsidosätta den genom att lägga till egna [ `where...timestamp...` ](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) -sats i frågan.)
3. Klicka på OK om du vill köra frågan.
4. Placera inte tomma rader i frågan. Du kan behålla flera separata frågor i en fråga flik genom att avgränsa dem med tomma rader. Den fråga som har markören körs.

### <a name="save-a-query"></a>Spara en fråga
![Spara en fråga](./media/app-insights-analytics-using/140.png)

1. Spara den aktuella fråga-filen.
2. Öppna en sparad fråga-fil.
3. Skapa en ny fråga-fil.

## <a name="see-the-details"></a>Visa detaljerad information
Expandera alla rader i de om du vill visa den fullständiga listan över egenskaper. Ytterligare kan du expandera en egenskap som är strukturerade värde: till exempel, anpassade dimensioner eller stacken lista i ett undantag.

![Expandera en rad](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Ordna resultaten
Du kan sortera, filtrera, sidbryta och grupperar resultaten från frågan.

> [!NOTE]
> Sortering, gruppering och filtrering i webbläsaren om du kör inte frågan igen. De endast ordna om resultaten som returnerades av din senaste fråga. 
> 
> Om du vill utföra åtgärderna i servern innan resultaten returneras, skriva en fråga med den [sortera](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [sammanfatta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) och [där](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) operatörer.
> 
> 

Välj de kolumner som du vill se, dra kolumnrubrikerna Om du vill ordna om dem och ändra storlek på kolumner genom att dra gränserna.

![Ordna kolumner](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Sortera och filtrera objekt
Sortera resultaten genom att klicka på chefen för en kolumn. Klicka igen om du vill sortera på andra sätt och på tredje tid att återställa till den ursprungliga sorteringen som returneras av frågan.

Använd filterikonen för att begränsa din sökning.

![Sortera och filtrera kolumner](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Gruppera objekt
Om du vill sortera efter mer än en kolumn genom att använda gruppering. Först aktivera den och dra kolumnrubrikerna i området ovanför tabellen.

![Grupp](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Saknar vissa resultat?

Om du tror att du inte kan se alla resultat som du förväntade dig, finns det några möjliga orsaker.

* **Intervallet tidsfiltret**. Som standard visas endast resultat från de senaste 24 timmarna. Det finns en automatisk filter som begränsar vilka resultat som hämtas från källtabellerna. 

    Du kan dock ändra tidsintervallet filtret med hjälp av den nedrullningsbara menyn.

    Eller du kan åsidosätta det automatiska intervallet genom att inkludera egna [ `where  ... timestamp ...` satsen](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) i frågan. Exempel:

    `requests | where timestamp > ago('2d')`

* **Resultaten gränsen**. Det finns en gräns på cirka 10 k rader på resultaten från portalen. En varning visas om du går över gränsen. Om detta händer visar sortering av resultaten i tabellen inte alltid alla faktiska första eller sista resultat. 

    Det är bästa praxis bör du undvika att träffa gränsen. Ange intervallet tidsfiltret eller operatorer som:

  * [de 100 främsta av tidsstämpel](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [ta 100](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [Sammanfatta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [där tidsstämpel > ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(Vill ha mer än 10 k raderna? Överväg att använda [löpande Export](app-insights-export-telemetry.md) i stället. Analytics är utformad för analys, i stället för hämtning rådata.)

## <a name="diagrams"></a>Diagram
Välj typ av diagram som du vill:

![Välj en diagramtyp av](./media/app-insights-analytics-using/230.png)

Om du har flera kolumner med rätt typer kan välja du x och y-axlarna och av dimensioner som du vill dela resultatet av en kolumn.

Som standard visas först som en tabell och du väljer diagrammet manuellt. Men du kan använda den [återge direktiv](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) i slutet av en fråga för att välja ett diagram.

### <a name="analytics-diagnostics"></a>Analytics-diagnostik


Om det finns en plötslig insamling eller steg i dina data på en timechart kan du se en markerad punkt på raden. Detta anger att Analytics-diagnostik har identifierats av en kombination av egenskaper som filtrerar ut plötslig förändring. Klicka på plats för att få mer information på filtret och se den filtrerade versionen. Detta kan hjälpa dig att identifiera vad som orsakade ändringen. 

[Mer information om Analytics diagnostik](app-insights-analytics-diagnostics.md)


![Analytics-diagnostik](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Fäst vid instrumentpanelen
Du kan fästa ett diagram eller en tabell till en av dina [delade instrumentpaneler](app-insights-dashboards.md) -Klicka bara på PIN-koden. 

![Klicka på PIN-koden](./media/app-insights-analytics-using/pin-01.png)

Det innebär att när du sätter ihop en instrumentpanel som hjälper dig att övervaka prestanda och användning av web services, kan du inkludera ganska komplex analys tillsammans med andra mått. 

Du kan fästa en tabell till instrumentpanelen, om den har fyra eller färre kolumner. Endast de översta sju raderna visas.

### <a name="dashboard-refresh"></a>Uppdatera instrumentpanelen
Diagrammet fäst på instrumentpanelen uppdateras automatiskt genom att köra frågan igen cirka varje timme. Du kan också klicka på knappen Uppdatera.

### <a name="automatic-simplifications"></a>Automatisk förenklingar

Vissa förenklingar tillämpas på ett diagram om du har Fäst det på en instrumentpanel.

**Tid begränsning:** frågor begränsas automatiskt till de senaste 14 dagarna. Den har samma effekten som om frågan innehåller `where timestamp > ago(14d)`.

**Bin antal begränsning:** om du visar ett diagram med diskreta lagerplatser (vanligtvis ett stapeldiagram) mycket mindre ifyllda lagerplatser grupperas automatiskt till en enda bin ”andra”. Till exempel den här frågan:

    requests | summarize count_search = count() by client_CountryOrRegion

ser ut så här i Analytics:

![Diagram med lång pilslut](./media/app-insights-analytics-using/pin-07.png)

men när du har Fäst det på en instrumentpanel, det ser ut så här:

![Diagram med begränsad lagerplatser](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Exportera till Excel
När du har kört en fråga, kan du hämta en CSV-fil. Klicka på **Exportera Excel**.

## <a name="export-to-power-bi"></a>Exportera till Power BI
Placera markören i en fråga och välj **exportera Power BI**.

![Exportera från Analytics till Powerbi](./media/app-insights-analytics-using/240.png)

Du kör frågan i Power BI. Du kan ange att uppdatera enligt ett schema.

Du kan skapa instrumentpaneler som samordnar data från olika källor med Power BI.

[Mer information om export till Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Djuplänk

Hämta en länk under **Export resursen länken** som du kan skicka till en annan användare. Förutsatt att användaren har [åtkomst till resursgruppen](app-insights-resources-roles-access-control.md), frågan öppnas i Användargränssnittet för analys.

(I länken Frågetexten visas efter ”? q =”, gzip komprimeras och Base64-kodad. Du kan skriva kod för att generera djuplänkar som du anger för användare. Det rekommenderade sättet att köra Analytics från kod är dock med hjälp av den [REST API](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automation

Använd den [Data Access REST API: et](https://dev.applicationinsights.io/) kör Analytics-frågor. [Till exempel](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (med PowerShell):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

Till skillnad från Gränssnittet för Analytics REST-API lägger inte automatiskt till alla tidsstämpel begränsningar till dina frågor. Kom ihåg att lägga till egna where-satsen, om du vill undvika stora svar.



## <a name="import-data"></a>Importera data

Du kan importera data från en CSV-fil. En typisk användning är att importera statiska data som du kan ansluta med tabeller från din telemetri. 

Om autentiserade användare identifieras i din telemetri av ett alias eller dolda id, kan du importera en tabell som mappar alias till verkliga namn. Genom att utföra en koppling på begärandetelemetri kan identifiera du användare efter verkliga namn i Analytics-rapporter.

### <a name="define-your-data-schema"></a>Definiera dataschemat

1. Klicka på **inställningar** (längst upp till vänster) och sedan **datakällor**. 
2. Lägg till en datakälla, följa anvisningarna. Du uppmanas att ange ett exempel på data som ska innehålla minst tio rader. Du kan sedan Korrigera schemat.

Detta definierar en datakälla där du kan importera enskilda tabeller.

### <a name="import-a-table"></a>Importera en tabell

1. Öppna din definition av datakällan i listan.
2. Klicka på ”ladda upp” och följ instruktionerna för att ladda upp tabellen. Detta innebär att ett anrop till en REST-API och är så enkelt att automatisera. 

Din tabell är nu tillgänglig för användning i Analytics-frågor. Den visas i Analytics 

### <a name="use-the-table"></a>Använd tabell

Anta att dina definitionen av datakällan anropas `usermap`, och att det finns två fält `realName` och `user_AuthenticatedId`. Den `requests` tabellen har också ett fält med namnet `user_AuthenticatedId`, så det är lätt att ansluta dem:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
Den resulterande tabellen för förfrågningar har en ytterligare kolumn `realName`.

### <a name="import-from-logstash"></a>Importera från LogStash

Om du använder [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), du kan använda Analytics för att fråga loggarna. Använd den [plugin-program som kommer data i Analytics](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

