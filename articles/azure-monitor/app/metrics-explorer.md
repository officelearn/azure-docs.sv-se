---
title: Utforska mått i Azure Application Insights | Microsoft-dokument
description: Hur du tolkar diagram på måttutforskaren och hur du anpassar metriska explorer-blad.
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: f85a8fe79e7f4f820d7c0e5b942730305e892095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275898"
---
# <a name="exploring-metrics-in-application-insights"></a>Utforska mätvärden i programinsikter
Mått i [Application Insights][start] är uppmätta värden och antal händelser som skickas i telemetri från ditt program. De hjälper dig att upptäcka prestandaproblem och titta på trender i hur ditt program används. Det finns ett brett utbud av standardmått och du kan också skapa egna anpassade mått och händelser.

> [!NOTE]
> I den här artikeln beskrivs den klassiska statistikutforskaren som för närvarande är föråldrad och så småningom kommer att dras tillbaka. Vi rekommenderar att du checkar ut den nya upplevelsen som beskrivs i [den här artikeln](../platform/metrics-charts.md).

Mått och händelseantal visas i diagram över aggregerade värden som summor, medelvärden eller antal.

Här är en exempeluppsättning diagram:

![](./media/metrics-explorer/01-overview.png)

Du hittar måttdiagram överallt i application insights-portalen. I de flesta fall kan de anpassas och du kan lägga till fler diagram i bladet. Från bladet Översikt klickar du vidare till mer detaljerade diagram (som har titlar som "Servrar") eller klickar på **Statistikutforskaren** för att öppna ett nytt blad där du kan skapa anpassade diagram.

## <a name="time-range"></a>Tidsintervall
Du kan ändra det tidsintervall som täcks av diagrammen eller rutnäten på valfritt blad.

![Öppna översiktsbladet för ditt program i Azure-portalen](./media/metrics-explorer/03-range.png)

Om du förväntar dig vissa data som inte har dykt upp ännu klickar du på Uppdatera. Diagram uppdatera sig med jämna mellanrum, men intervallen är längre för större tidsintervall. Det kan ta ett tag innan data kommer genom analyspipelinen till ett diagram.

Om du vill zooma in i en del av ett diagram drar du över det:

![Dra över en del av ett diagram.](./media/metrics-explorer/12-drag.png)

Klicka på knappen Ångra zoom för att återställa den.

## <a name="granularity-and-point-values"></a>Granularitet och punktvärden
Håll musen över diagrammet för att visa värdena för måtten vid den tidpunkten.

![Håll muspekaren över ett diagram](./media/metrics-explorer/02-focus.png)

Värdet för måttet vid en viss punkt aggregeras över föregående samplingsintervall.

Provtagningsintervallet eller "granulariteten" visas högst upp på bladet.

![Huvudet på ett blad.](./media/metrics-explorer/11-grain.png)

Du kan justera granulariteten i bladet Tidsintervall:

![Huvudet på ett blad.](./media/metrics-explorer/grain.png)

Vilka granulariteter som är tillgängliga beror på vilket tidsintervall du väljer. De explicita granulariteterna är alternativ till den "automatiska" granulariteten för tidsintervallet.


## <a name="editing-charts-and-grids"></a>Redigera diagram och rutnät
Så här lägger du till ett nytt diagram i bladet:

![I Statistikutforskaren väljer du Lägg till diagram](./media/metrics-explorer/04-add.png)

Välj **Redigera** i ett befintligt eller nytt diagram om du vill redigera det som visas:

![Välj ett eller flera mått](./media/metrics-explorer/08-select.png)

Du kan visa mer än ett mått i ett diagram, men det finns begränsningar för de kombinationer som kan visas tillsammans. Så fort du väljer ett mått inaktiveras några av de andra.

Om du kodade [anpassade mått][track] i din app (samtal till TrackMetric och TrackEvent) visas de här.

## <a name="segment-your-data"></a>Segmentera dina data
Du kan dela ett mått efter egenskap , till exempel för att jämföra sidvisningar på klienter med olika operativsystem.

Markera ett diagram eller rutnät, aktivera gruppering och välj en egenskap som ska grupperas efter:

![Välj Gruppera på och ange sedan en egenskap i Gruppera efter](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> När du använder gruppering innehåller diagramtyperna Område och stapel en staplad skärm. Detta är lämpligt där aggregeringsmetoden är Summa. Men där aggregeringstypen är Medel väljer du visningstyperna Linje eller Rutnät.
>
>

Om du kodade [anpassade mått][track] i din app och de innehåller egenskapsvärden kan du välja egenskapen i listan.

Är diagrammet för litet för segmenterade data? Justera dess höjd:

![Justera skjutreglaget](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Aggregeringstyper
Förklaringen vid sidan visar som standard vanligtvis det aggregerade värdet under diagramperioden. Om du hovrar över diagrammet visas värdet vid den tidpunkten.

Varje datapunkt i diagrammet är en sammanställning av de datavärden som mottagits under föregående samplingsintervall eller "granularitet". Granulariteten visas högst upp på bladet och varierar med diagrammets övergripande tidsskala.

Mått kan aggregeras på olika sätt:

* **Antal** är ett antal händelser som tas emot i samplingsintervallet. Den används för händelser som begäranden. Variationer i diagrammets höjd anger variationer i den hastighet med vilken händelserna inträffar. Men observera att det numeriska värdet ändras när du ändrar samplingsintervallet.
* **Summer lägger** till värdena för alla datapunkter som tas emot under samplingsintervallet eller diagrammets period.
* **Medelvärdet** dividerar summan med antalet datapunkter som tas emot över intervallet.
* **Unika** antal används för antal användare och konton. Under samplingsintervallet, eller under diagrammets period, visar figuren antalet olika användare som setts under den tiden.
* **%**- procentversioner av varje aggregering används endast med segmenterade diagram. Summan uppgår alltid till 100 % och diagrammet visar det relativa bidraget för olika komponenter av en summa.

    ![Procentuell aggregering](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Ändra aggregeringstyp

![Redigera diagrammet och välj sedan Aggregering](./media/metrics-explorer/05-aggregation.png)

Standardmetoden för varje mått visas när du skapar ett nytt diagram eller när alla mått är avmarkerade:

![Avmarkera alla mått för att se standardvärdena](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Pin Y-axeln 
Som standard visar ett diagram Y-axelvärden från noll till högsta värden i dataområdet, för att ge en visuell representation av kvantum av värdena. Men i vissa fall mer än den kvantmekaniska det kan vara intressant att visuellt inspektera mindre förändringar i värden. För anpassningar som denna använder du Y-axelns områdesredigeringsfunktion för att fästa lägsta eller högsta värde på Y-axeln på önskad plats.
Klicka på kryssrutan "Avancerade inställningar" för att visa Y-axelns intervallinställningar

![Klicka på Avancerade inställningar, välj Anpassat område och ange maxvärden för min](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrera dina data
Så här visar du bara måtten för en vald uppsättning egenskapsvärden:

![Klicka på Filter, expandera en egenskap och kontrollera vissa värden](./media/metrics-explorer/19-filter.png)

Om du inte väljer några värden för en viss egenskap är det samma sak som att välja dem alla: det finns inget filter på den egenskapen.

Lägg märke till antalet händelser vid sidan av varje egenskapsvärde. När du väljer värden för en egenskap justeras antalet tillsammans med andra egenskapsvärden.

Filter gäller för alla diagram på ett blad. Om du vill att olika filter ska tillämpas på olika diagram skapar och sparar du olika måttblad. Om du vill kan du fästa diagram från olika blad på instrumentpanelen, så att du kan se dem bredvid varandra.

### <a name="remove-bot-and-web-test-traffic"></a>Ta bort bot- och webbtesttrafik
Använd filtret **Verklig eller syntetisk trafik** och kontrollera **Real**.

Du kan också filtrera efter **källa till syntetisk trafik**.

### <a name="to-add-properties-to-the-filter-list"></a>Så här lägger du till egenskaper i filterlistan
Vill du filtrera telemetri på en kategori som du själv väljer? Du kanske till exempel delar upp användarna i olika kategorier och vill segmentera dina data efter dessa kategorier.

[Skapa din egen egendom](../../azure-monitor/app/api-custom-events-metrics.md#properties). Ställ in den i en [telemetriinitierare](../../azure-monitor/app/api-custom-events-metrics.md#defaults) så att den visas i all telemetri – inklusive standardelemetri som skickas av olika SDK-moduler.

## <a name="edit-the-chart-type"></a>Redigera diagramtypen
Observera att du kan växla mellan rutnät och diagram:

![Markera ett rutnät eller diagram och välj sedan en diagramtyp](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Spara bladet för mätvärden
När du har skapat några diagram sparar du dem som favoriter. Du kan välja om du vill dela den med andra gruppmedlemmar om du använder ett organisationskonto.

![Välj favorit](./media/metrics-explorer/21-favorite-save.png)

Om du vill se bladet igen **går du till översiktsbladet** och öppnar Favoriter:

![Välj Favoriter i bladet Översikt](./media/metrics-explorer/22-favorite-get.png)

Om du väljer Relativt tidsintervall när du sparade uppdateras bladet med de senaste måtten. Om du väljer Absolut tidsintervall visas samma data varje gång.

## <a name="reset-the-blade"></a>Återställ bladet
Om du redigerar ett blad men sedan vill gå tillbaka till den ursprungliga sparade uppsättningen klickar du bara på Återställ.

![I knapparna högst upp i Metric Explorer](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live mått ström

För en mycket mer omedelbar bild av din telemetri, öppna [Live Stream](live-stream.md). De flesta mått tar några minuter att visas, på grund av processen för aggregering. Däremot är livemått optimerade för låg latens. 

## <a name="set-alerts"></a>Ange aviseringar
Om du vill meddelas via e-post om ovanliga värden för ett mått lägger du till en avisering. Du kan välja att antingen skicka e-postmeddelandet till kontoadministratörerna eller till specifika e-postadresser.

![I Statistikutforskaren väljer du Varningsregler, Lägg till avisering](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Läs mer om aviseringar][alerts].


## <a name="continuous-export"></a>Kontinuerlig export
Om du vill att data ska exporteras kontinuerligt så att du kan bearbeta dem externt kan du överväga att använda [Kontinuerlig export](../../azure-monitor/app/export-telemetry.md).

### <a name="power-bi"></a>Power BI
Om du vill ha ännu mer rikare vyer av dina data kan du [exportera till Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analys
[Analytics](../../azure-monitor/app/analytics.md) är ett mer mångsidigt sätt att analysera din telemetri med ett kraftfullt frågespråk. Använd den om du vill kombinera eller beräkna resultat från mått, eller utföra en djupgående utforskning av appens senaste prestanda. 

Från ett måttdiagram kan du klicka på Analytics-ikonen för att komma direkt till motsvarande Analytics-fråga.

## <a name="troubleshooting"></a>Felsökning
*Jag ser inga data i diagrammet.*

* Filter gäller för alla diagram på bladet. Se till att du inte har angett något filter som utesluter alla data på ett annat när du fokuserar på ett diagram.

    Om du vill ställa in olika filter på olika diagram skapar du dem i olika blad och sparar dem som separata favoriter. Om du vill kan du fästa dem på instrumentpanelen så att du kan se dem bredvid varandra.
* Om du grupperar ett diagram efter en egenskap som inte har definierats i måttet finns det ingenting i diagrammet. Prova att rensa "gruppera efter" eller välj en annan grupperingsegenskap.
* Prestandadata (CPU, IO-hastighet och så vidare) är tillgängliga för Java-webbtjänster, Windows-skrivbordsappar, [IIS-webbappar och -tjänster om du installerar statusövervakare](../../azure-monitor/app/monitor-performance-live-website-now.md)och [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). Den är inte tillgänglig för Azure-webbplatser.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
* [Övervaka användning med Application Insights](../../azure-monitor/app/usage-overview.md)
* [Använda diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
