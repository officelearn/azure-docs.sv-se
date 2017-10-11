---
title: "Utforska mätvärden i Azure Application Insights | Microsoft Docs"
description: "Så här tolkar diagram på mått explorer och hur du anpassar mått explorer blad."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: bwren
ms.openlocfilehash: a13500284caab79bbe221060ccf3d925ffb1fab5
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="exploring-metrics-in-application-insights"></a>Utforska mått i Application Insights
Mått i [Programinsikter] [ start] mätvärden och antalet händelser som skickas i telemetri från ditt program. De hjälper dig att identifiera prestandaproblem med och titta på trender i hur programmet används. Det finns en mängd olika standard mått och du kan också skapa egna anpassade mått och händelser.

Antal mått och händelse visas i diagram av mätvärden, till exempel summor, medelvärden eller antal.

Här är ett exempel uppsättning diagram:

![](./media/app-insights-metrics-explorer/01-overview.png)

Du hittar mått diagram överallt i Application Insights-portalen. I de flesta fall kan anpassas och du kan lägga till fler diagram bladet. I bladet översikt Klicka vidare till mer detaljerad diagram (som har titlar, till exempel ”servrar”), eller klicka på **Metrics Explorer** att öppna ett nytt blad där du kan skapa anpassade diagram.

## <a name="time-range"></a>Tidsintervall
Du kan ändra det tidsintervall som omfattas av diagram eller rutnät på ett blad.

![Öppna bladet översikt av ditt program i Azure-portalen](./media/app-insights-metrics-explorer/03-range.png)

Klicka på Uppdatera om du väntar vissa data som inte fanns ännu. Diagram uppdatera sig själva med intervall, men intervall är längre för större tidsintervall. Det kan ta en stund innan data att komma via analyser pipeline till ett diagram.

Dra över den för att zooma in en del av ett diagram:

![Dra över en del av ett diagram.](./media/app-insights-metrics-explorer/12-drag.png)

Klicka på knappen Ångra Zooma om du vill återställa den.

## <a name="granularity-and-point-values"></a>Granularitet och punkt värden
Placera pekaren över diagrammet för att visa värdena i mätvärdena som vid den punkten.

![Placera markören över ett diagram](./media/app-insights-metrics-explorer/02-focus.png)

Värdet för måttet vid en viss slås samman under föregående exempelintervallet.

Exempelintervall eller ”granularitet” visas längst upp på bladet.

![Rubriken för ett blad.](./media/app-insights-metrics-explorer/11-grain.png)

Du kan justera granularitet i bladet tid intervall:

![Rubriken för ett blad.](./media/app-insights-metrics-explorer/grain.png)

De tillgängliga kornigheterna beror på vilket tidsintervall du väljer. Explicit granulariteter är alternativ till ”automatisk” Granulariteten för tidsintervallet.


## <a name="editing-charts-and-grids"></a>Redigera diagram och rutnät
Lägga till ett nytt diagram i bladet:

![Välj Lägg till diagram i Metrics Explorer](./media/app-insights-metrics-explorer/04-add.png)

Välj **redigera** på en befintlig eller ny diagram för att redigera den visar:

![Välj ett eller flera mått](./media/app-insights-metrics-explorer/08-select.png)

Du kan visa fler än ett mått i ett diagram, även om det finns begränsningar om kombinationer som kan visas tillsammans. När du väljer ett enskilt mått är några av de andra inaktiverade.

Om du har kodats [anpassade mått] [ track] i din app (anrop till TrackMetric och TrackEvent) visas här.

## <a name="segment-your-data"></a>Segmentera dina data
Du kan dela ett mått med egenskapen – till exempel om du vill jämföra sidvyer på klienter med olika operativsystem.

Välj ett diagram eller rutnät, aktivera gruppering och välj en egenskap som ska grupperas efter:

![Välj gruppering på och ange en egenskap i Group By](./media/app-insights-metrics-explorer/15-segment.png)

> [!NOTE]
> När du använder gruppering ger området och stapeldiagram typer en staplad visning. Detta är lämpligt där metoden aggregering summan. Men om Aggregeringstyp medelvärde, välja vilka raden eller rutnät visas.
>
>

Om du har kodats [anpassade mått] [ track] i din app och de innehåller egenskapsvärden, du kommer att kunna markerar du egenskapen i listan.

Är ett diagram för liten för segmenterade data? Justera höjden:

![Justera skjutreglaget](./media/app-insights-metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Aggregeringen typer
Förklaring till sidan som standard visas vanligtvis aggregerade värde under diagrammet. Om du hovrar över diagrammet visar värdet vid den punkten.

Varje datapunkt i diagrammet är en aggregering av datavärden i föregående insamlingsintervall eller ”granularitet”. Granulariteten varierar beroende på den övergripande tidsrymd i diagrammet och visas längst upp på bladet.

Mått kan sammanställas på olika sätt:

* **Antal** antal händelser mottagna på exempelintervallet. Den används för händelser, till exempel begäranden. Variationer i höjden av diagrammet anger variationer i händelserna ska inträffa. Men Observera att det numeriska värdet ändras när du ändrar exempelintervallet.
* **Summa** lägger till värden för alla datapunkter som tagits emot över exempelintervallet eller punkt i diagrammet.
* **Genomsnittlig** delas summan av antalet datapunkter som togs emot under period.
* **Unik** antal används för antal användare och konton. Över exempelintervallet eller under diagrammet visar bilden antalet för olika användare visas i den tiden.
* **%**-procent versioner av varje aggregerings kan bara användas med segmenterade diagram. Det totala antalet läggs alltid upp till 100% och i diagrammet visas olika komponenterna i totalt relativa bidrag.

    ![Procentandel aggregering](./media/app-insights-metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Ändra Aggregeringstyp

![Redigera diagrammet och välj sedan aggregering](./media/app-insights-metrics-explorer/05-aggregation.png)

Standardmetoden för varje mått visas när du skapar ett nytt schema eller när alla är avmarkerat:

![Avmarkera alla mått att visa standardinställningar](./media/app-insights-metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>PIN-kod y-axeln 
Som standard visar ett diagram Y-axeln värden med början från noll till maxvärdena i dataområdet att ge en bild av quantum av värden. Men i vissa fall mer än quantum kan det vara intressant att visuellt inspektera mindre ändringar i värden. Y-axeln intervallet för anpassningar som den här användningen redigera funktionen fästa y-axeln lägsta eller högsta värdet på rätt plats.
Klicka på ”Avancerade inställningar” kryssrutan för att få fram det y-axel intervallet inställningar

![Klicka på Avancerade inställningar, välja eget intervall och ange min maxvärde](./media/app-insights-metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrera dina data
Visa bara mått för en vald uppsättning egenskapsvärden:

![Klicka på filtret, expandera en egenskap och kontrollera vissa värden](./media/app-insights-metrics-explorer/19-filter.png)

Om du inte markerar alla värden för en viss egenskap, är det samma som att markera dem: det finns inget filter på egenskapen.

Observera att antalet händelser tillsammans med varje egenskapsvärde. När du väljer värden för en egenskap, justeras antalet tillsammans med andra egenskapsvärden.

Filter tillämpas på alla diagram på ett blad. Om du vill att olika filter som används för olika diagram, skapa och spara olika mått blad. Om du vill kan kan du fästa diagram från olika blad på instrumentpanelen så att du kan se dem tillsammans med varandra.

### <a name="remove-bot-and-web-test-traffic"></a>Ta bort bot och web test-trafik
Med filtret **verklig eller syntetisk trafik** och kontrollera **verkliga**.

Du kan också filtrera efter **källa för syntetisk trafik**.

### <a name="to-add-properties-to-the-filter-list"></a>Att lägga till egenskaper i filterlistan
Vill du filtrera telemetri för en kategori väljer själv? Exempelvis kanske du dela upp användarna i olika kategorier och du vill att segmentera dina data med dessa kategorier.

[Skapa en egen egenskap](app-insights-api-custom-events-metrics.md#properties). Ange den i en [telemetri initieraren](app-insights-api-custom-events-metrics.md#defaults) så att det visas i alla telemetri - inklusive standard telemetri som skickas av olika SDK-moduler.

## <a name="edit-the-chart-type"></a>Redigera diagramtypen
Observera att du kan växla mellan rutnät och diagram:

![Markera ett rutnät eller diagram och välj en diagramtyp](./media/app-insights-metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Spara mått-bladet
När du har skapat vissa diagram kan du spara dem som en favorit. Du kan välja att dela den med andra medlemmar, om du använder ett organisationskonto.

![Välj favorit](./media/app-insights-metrics-explorer/21-favorite-save.png)

Visa bladet igen, **gå till bladet översikt** och öppna Favoriter:

![I bladet översikt välja Favoriter](./media/app-insights-metrics-explorer/22-favorite-get.png)

Om du väljer relativt tidsintervall när du har sparat uppdateras bladet med senaste mått. Om du väljer absolut tidsintervall visas samma data varje gång.

## <a name="reset-the-blade"></a>Återställ bladet
Om du redigerar ett blad, men du vill gå tillbaka till den ursprungliga sparat, klickar du på Återställ.

![I knapparna överst i måttet Explorer](./media/app-insights-metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live mått dataström

En mycket mer omedelbar vy över din telemetri öppna [Liveströmning](app-insights-live-stream.md). De flesta mått ta några minuter innan den visas på grund av processen för aggregering. Däremot är live mått optimerade för låg latens. 

## <a name="set-alerts"></a>Ange aviseringar
Lägga till en avisering om du vill meddelas via e-post med ovanliga värden för alla mått. Du kan välja att skicka e-postmeddelandet till kontoadministratörer eller till specifika e-postadresser.

![Välj Varningsregler, Lägg till avisering i Metrics Explorer](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[Mer information om aviseringar][alerts].


## <a name="continuous-export"></a>Kontinuerlig export
Om du vill att data exporterats kontinuerligt så att du kan bearbeta externt, bör du använda [löpande export](app-insights-export-telemetry.md).

### <a name="power-bi"></a>Power BI
Om du vill att ännu bättre vyer för dina data, kan du [exportera till Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analys
[Analytics](app-insights-analytics.md) mer flexibla kan analysera dina telemetri med hjälp av ett kraftfullt frågespråk. Använd det om du vill kombinera compute resultat från mått eller utföra en ingående undersökning av din app senaste prestanda. 

Från ett mått diagram, kan du klicka på ikonen Analytics direkt till motsvarande Analytics-fråga.

## <a name="troubleshooting"></a>Felsökning
*Alla data visas inte i diagrammet.*

* Filter tillämpas på alla diagram på bladet. Se till att även om du fokusera på ett diagram, utan att ställa in ett filter som utesluter alla data på en annan.

    Om du vill ange olika filter på olika diagram, skapa dem i olika blad, spara dem som separata Favoriter. Om du vill kan fästa du dem på instrumentpanelen så att du kan se dem tillsammans med varandra.
* Om du grupperar ett diagram av en egenskap som inte har definierats för måttet att kommer det finnas ingenting i diagrammet. Prova att rensa 'group by- eller välj en annan gruppering-egenskap.
* Prestandadata (CPU, IO-frekvens och så vidare) är tillgängligt för Java-webbtjänster, Windows-skrivbordsappar [av IIS-program och tjänster om du installerar statusövervakaren](app-insights-monitor-performance-live-website-now.md), och [Azure Cloud Services](app-insights-azure.md). Det är inte tillgänglig för Azure websites.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
* [Övervakning med Application Insights](app-insights-web-track-usage.md)
* [Diagnostiska sökning](app-insights-diagnostic-search.md)

<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
