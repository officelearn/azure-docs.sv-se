---
title: Utforska mått i Azure Application Insights | Microsoft Docs
description: Så här tolkar du diagrammen på metric explorer och hur du anpassar metric explorer-blad.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/08/2017
ms.author: mbullwin
ms.openlocfilehash: 6341ffe33984a7f40f9d4120ca6726a082463474
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039715"
---
# <a name="exploring-metrics-in-application-insights"></a>Utforska mått i Application Insights
Mått i [Programinsikter] [ start] är mätvärden och antalet händelser som skickas i telemetri från ditt program. De hjälper dig att identifiera problem med prestanda och se trender i hur ditt program används. Det finns en mängd olika standardmått och du kan också skapa egna anpassade mått och händelser.

Antal mått och händelse visas i diagram över sammanställda värden, till exempel summor, medelvärden eller antal.

Här är ett exempel uppsättningen diagram:

![](./media/metrics-explorer/01-overview.png)

Du hittar överallt måttdiagram i Application Insights-portalen. I de flesta fall kan anpassas och du kan lägga till fler diagram till bladet. Från bladet översikt Klicka vidare till mer detaljerad diagram (som har rubriker, till exempel ”servrar”), eller klicka på **Metrics Explorer** att öppna ett nytt blad där du kan skapa anpassade diagram.

## <a name="time-range"></a>Tidsintervall
Du kan ändra tidsintervallet som omfattas av diagram eller rutnät på ett blad.

![Öppna översiktsbladet för ditt program i Azure portal](./media/metrics-explorer/03-range.png)

Klicka på Uppdatera om du väntade vissa data som inte visas än. Diagrammen uppdateras automatiskt med mellanrum, men intervall som är längre för större tidsintervall. Det kan ta en stund innan data ska hämtas genom analys av pipelinen i ett schema.

Om du vill zooma in en del av ett diagram, drar du över den:

![Dra över en del av ett diagram.](./media/metrics-explorer/12-drag.png)

Klicka på knappen Ångra Zooma om du vill återställa den.

## <a name="granularity-and-point-values"></a>Granularitet och punkt värden
Håller muspekaren över diagrammet för att visa värdena i mått som i det här läget.

![Hovra med musen över ett diagram](./media/metrics-explorer/02-focus.png)

Värdet för måttet vid en viss slås samman under föregående exempelintervallet.

Exempelintervall eller ”kornighet” visas längst upp på bladet.

![Rubrik för ett blad.](./media/metrics-explorer/11-grain.png)

Du kan justera precisionen i bladet tid intervall:

![Rubrik för ett blad.](./media/metrics-explorer/grain.png)

De tillgängliga kornigheterna beror på det tidsintervall som du väljer. Explicit Precision är alternativ till ”automatisk” Granulariteten för intervallet.


## <a name="editing-charts-and-grids"></a>Redigera diagram och rutnät
Lägga till ett nytt diagram till bladet:

![Välj Lägg till diagram i Metrics Explorer](./media/metrics-explorer/04-add.png)

Välj **redigera** på en befintlig eller ny diagrammet för att redigera vad som visas:

![Välj ett eller flera mått](./media/metrics-explorer/08-select.png)

Du kan visa fler än ett mått i ett diagram, även om det finns begränsningar om kombinationer som kan visas tillsammans. När du väljer ett enskilt mått, har några av de andra inaktiverats.

Om du kodade [anpassade mått] [ track] i din app (anrop till TrackMetric och TrackEvent) visas här.

## <a name="segment-your-data"></a>Segmentera dina data
Du kan dela ett mått med egenskapen – till exempel om du vill jämföra sidvisningar på klienter med olika operativsystem.

Välj ett diagram eller rutnät, växla gruppering och välja en egenskap att gruppera efter:

![Välj gruppering på och ange en egenskap i Group By](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> När du använder gruppering kan ange vilka yt- och stapeldiagram en stående visning. Detta är lämpligt där sammansättningsmetoden summan. Men där aggregeringstypen är medelvärde, välja vilka linje- eller visa.
>
>

Om du kodade [anpassade mått] [ track] i din app och de innehåller egenskapsvärden kommer du att kunna välja egenskapen i listan.

Är diagrammet för liten för segmenterade data? Justera kontrollens höjd:

![Justera skjutreglaget](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Aggregeringstyper
Förklaringen på sida som standard visar vanligtvis aggregerat värde under diagrammet. Om du hovrar över diagrammet visar värdet i det här läget.

Varje datapunkt i diagrammet är en mängd av datavärden i föregående exempelintervall eller ”kornighet”. Precisionen visas längst upp på bladet och varierar beroende på övergripande tidsskalan i diagrammet.

Mått kan aggregeras på olika sätt:

* **Antal** är ett antal händelser som har tagits emot under exempelintervallet. Den används för händelser, till exempel begäranden. Variationer i höjden av diagrammet anger variationer i händelserna ska inträffa. Men Observera att det numeriska värdet ändras när du ändrar exempelintervallet.
* **Summa** summerar värdena för alla datapunkter som tas emot via exempelintervallet eller punkt i diagrammet.
* **Genomsnittlig** dividerar summan av antalet datapunkter som togs emot under period.
* **Unikt** antal används för antalet användare och konton. Under period för sampling, eller under perioden i diagrammet visar bilden antal olika användare som visas i den tiden.
* **%** -procent versioner av varje aggregerings används bara med segmenterade diagram. Det totala antalet lägger alltid upp till 100% och diagrammet visar olika komponenterna i totalt relativa bidrag.

    ![Procentuell sammansättning](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Ändra aggregeringstypen

![Redigera diagrammet och välj sedan aggregering](./media/metrics-explorer/05-aggregation.png)

Standardmetoden för varje mått visas när du skapar ett nytt diagram eller när alla mått är avmarkerat.

![Avmarkera alla mått för att se standardinställningarna](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>PIN-kod y-axeln 
Som standard visar ett diagram Y-axelvärden från noll till maxvärdena i dataområdet att ge en visuell representation av quantum av värden. Men i vissa fall som är mer än quantum och det kan vara intressant att visuellt inspektera mindre ändringar i värden. Anpassningar som den här användningen variera y-axeln redigeringsfunktion att fästa y-axeln lägsta eller högsta värdet på önskad plats.
Klicka på ”Avancerade inställningar” kryssrutan för att ta fram y-axelintervall inställningar

![Klicka på Avancerade inställningar, välja eget intervall och ange min maxvärden](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrera dina data
Visa bara mått för en vald uppsättning egenskapsvärden:

![Klicka på Filter, expandera en egenskap och kontrollera vissa värden](./media/metrics-explorer/19-filter.png)

Om du inte markerar alla värden för en viss egenskap, är det samma som att markera dem: det finns inget filter på egenskapen.

Observera att antalet händelser tillsammans med varje egenskapsvärde. När du väljer värden för en egenskap kan justeras antalet tillsammans med andra egenskapsvärden.

Filtren gäller för alla diagram på ett blad. Om du vill att olika filter som används för olika diagram, skapa och spara olika mått blad. Om du vill kan fästa du diagram från olika blad på instrumentpanelen så att du kan se dem tillsammans med varandra.

### <a name="remove-bot-and-web-test-traffic"></a>Ta bort bot och web testtrafik
Med filtret **verklig eller syntetisk trafik** och kontrollera **verkliga**.

Du kan också filtrera efter **källa för syntetisk trafik**.

### <a name="to-add-properties-to-the-filter-list"></a>Att lägga till egenskaper i filterlistan
Vill du filtrera telemetri på en kategori för en egen? Till exempel kanske du dela upp användarna i olika kategorier och du vill segmentera dina data med dessa kategorier.

[Skapa din egen egenskap](../../azure-monitor/app/api-custom-events-metrics.md#properties). Ange den i en [Telemetriinitieraren](../../azure-monitor/app/api-custom-events-metrics.md#defaults) så att det visas i all telemetri – inklusive standard telemetri som skickas av olika SDK-moduler.

## <a name="edit-the-chart-type"></a>Redigera diagramtyp
Observera att du kan växla mellan rutnät och diagram:

![Välj en rutnätet eller diagrammet och välj en diagramtyp](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Spara din måttbladet
När du har skapat vissa diagram kan du spara dem som en favorit. Du kan välja om du vill dela den med andra gruppmedlemmar om du använder ett organisationskonto.

![Välj favorit](./media/metrics-explorer/21-favorite-save.png)

Att se bladet igen och **gå till översiktsbladet** och öppna Favoriter:

![I bladet översikt Välj Favoriter](./media/metrics-explorer/22-favorite-get.png)

Om du har valt relativt tidsintervall när du har sparat uppdateras på bladet med de senaste mått. Om du valde absolut tidsintervall, visar det samma data varje gång.

## <a name="reset-the-blade"></a>Återställ bladet
Om du redigerar ett blad, men du vill gå tillbaka till den ursprungliga sparat, klickar du på Återställ.

![I knappar överst i Metric Explorer](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live metrics stream

En mycket mer omedelbar vy över telemetrin öppna [Live Stream](live-stream.md). De flesta måtten ta ett par minuter att visas på grund av processen för aggregering. Däremot är livemått optimerade för låg latens. 

## <a name="set-alerts"></a>Ange aviseringar
Lägg till en avisering om du vill meddelas via e-post med ovanliga värden för alla mått. Du kan välja att skicka e-postmeddelandet till kontoadministratörer eller till specifika e-postadresser.

![I Metrics Explorer väljer du Varningsregler, Lägg till avisering](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Läs mer om aviseringar][alerts].


## <a name="continuous-export"></a>Kontinuerlig export
Om du vill data kontinuerligt exporteras så att du kan bearbeta externt, bör du använda [löpande export](../../azure-monitor/app/export-telemetry.md).

### <a name="power-bi"></a>Power BI
Om du vill att ännu bättre vyer över dina data, kan du [exportera till Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analytics
[Analytics](../../azure-monitor/app/analytics.md) är ett mer flexibelt sätt att analysera din telemetri med hjälp av ett kraftfullt frågespråk. Använd det om du vill kombinera compute resultaten från mått eller utför en detaljerad förklaring av appens senaste prestanda. 

Du kan klicka på ikonen Analytics för att komma direkt till motsvarande Analytics-frågan från ett måttdiagram.

## <a name="troubleshooting"></a>Felsökning
*Jag ser några data i diagrammet.*

* Filtren gäller för alla diagram på bladet. Se till att även om du vill fokusera på ett diagram, ställa in ett filter som inte omfattar alla data på en annan.

    Om du vill definiera olika filter för olika diagram, skapa dem i olika bladen kan du spara dem som separata Favoriter. Om du vill kan fästa du dem på instrumentpanelen så att du kan se dem tillsammans med varandra.
* Om du grupperar ett diagram av en egenskap som inte har definierats för måttet kan sedan debiteras ingenting i diagrammet. Försök rensa ”Gruppera efter” eller välj en egenskap för olika grupperingsnivåer.
* Prestandadata (CPU, IO-frekvens och så vidare) är tillgängligt för Java-webbtjänster, Windows-skrivbordsappar, [IIS webbprogram och tjänster om du installerar statusövervakaren](../../azure-monitor/app/monitor-performance-live-website-now.md), och [Azure Cloud Services](../../application-insights/app-insights-overview.md). Det är inte tillgängligt för Azure-webbplatser.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
* [Övervakning med Application Insights](../../azure-monitor/app/usage-overview.md)
* [Med hjälp av Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../application-insights/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
