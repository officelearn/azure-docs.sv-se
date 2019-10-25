---
title: Utforska mått i Azure Application Insights | Microsoft Docs
description: Hur du tolkar diagram i metriska Utforskaren och hur du anpassar ett blad i mått Utforskaren.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/22/2019
ms.openlocfilehash: b0831ff500ba4cbe71dae6251fd960f6c96c0fe5
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820784"
---
# <a name="exploring-metrics-in-application-insights"></a>Utforska mått i Application Insights
Mått i [Application Insights][start] mäts värden och antalet händelser som skickas i telemetri från ditt program. De hjälper dig att identifiera prestanda problem och se trender i hur ditt program används. Det finns en mängd standard mått och du kan också skapa egna anpassade mått och händelser.

> [!NOTE]
> I den här artikeln beskrivs den klassiska Metrics Explorer-upplevelsen som för närvarande är föråldrad och kommer snart att dras tillbaka. Vi rekommenderar att du kontrollerar den nya upplevelsen som beskrivs i [den här artikeln](../platform/metrics-charts.md).

Mått och antal händelser visas i diagram över aggregerade värden, till exempel summor, medelvärden eller antal.

Här är en exempel uppsättning diagram:

![](./media/metrics-explorer/01-overview.png)

Du hittar mått diagram överallt i Application Insights portalen. I de flesta fall kan de anpassas och du kan lägga till fler diagram på bladet. Från översikts bladet klickar du till mer detaljerade diagram (som har titlar som "servrar") eller på **Metrics Explorer** för att öppna ett nytt blad där du kan skapa anpassade diagram.

## <a name="time-range"></a>Tidsintervall
Du kan ändra det tidsintervall som omfattas av diagrammet eller rutnätet på ett blad.

![Öppna bladet översikt för programmet i Azure Portal](./media/metrics-explorer/03-range.png)

Om du förväntar dig data som ännu inte har funnits klickar du på Uppdatera. Diagram uppdateras själva med intervall, men intervallen är längre för större tidsintervall. Det kan ta en stund innan data kommer genom analys pipelinen till ett diagram.

Om du vill zooma in i en del av ett diagram drar du över det:

![Dra över en del av ett diagram.](./media/metrics-explorer/12-drag.png)

Klicka på knappen Ångra zoomning för att återställa den.

## <a name="granularity-and-point-values"></a>Precisions-och punkt värden
Hovra musen över diagrammet för att visa värdena för måtten vid den punkten.

![Hovra med musen över ett diagram](./media/metrics-explorer/02-focus.png)

Värdet för måttet vid en viss punkt räknas samman över föregående samplings intervall.

Samplings intervallet eller "granularitet" visas överst på bladet.

![Rubriken på ett blad.](./media/metrics-explorer/11-grain.png)

Du kan justera granularitet på bladet tidsintervall:

![Rubriken på ett blad.](./media/metrics-explorer/grain.png)

Vilka detaljer som är tillgängliga beror på vilket tidsintervall du väljer. De uttryckliga granularitet är alternativ för "automatisk" precision för tidsintervallet.


## <a name="editing-charts-and-grids"></a>Redigera diagram och rutnät
Så här lägger du till ett nytt diagram på bladet:

![I Metrics Explorer väljer du Lägg till diagram](./media/metrics-explorer/04-add.png)

Välj **Redigera** i ett befintligt eller nytt diagram för att redigera vad som visas:

![Välj ett eller flera mått](./media/metrics-explorer/08-select.png)

Du kan visa fler än ett mått i ett diagram, även om det finns begränsningar för kombinationerna som kan visas tillsammans. Så snart du väljer ett mått är några av de andra inaktiverade.

Om du kodade [anpassade mått][track] till din app (anrop till TrackMetric och TrackEvent) visas de här.

## <a name="segment-your-data"></a>Segmentera dina data
Du kan dela ett mått efter egenskap, till exempel för att jämföra sidvyer på klienter med olika operativ system.

Välj ett diagram eller ett rutnät, växla över gruppering och välj en egenskap att gruppera efter:

![Välj Gruppera på och ange sedan Välj en egenskap i gruppera efter](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> När du använder gruppering ger yt-och stapel diagram en staplad visning. Detta är lämpligt där agg regerings metoden är Sum. Men om agg regerings typen är genomsnitt, väljer du visnings typer för linje eller rutnät.
>
>

Om du kodade [anpassade mått][track] till din app och de innehåller egenskaps värden kan du välja egenskapen i listan.

Är diagrammet för litet för segmenterade data? Justera dess höjd:

![Justera skjutreglaget](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Agg regerings typer
Förklaringen på sidan som standard visar vanligt vis det sammanlagda värdet för diagrammets tid. Om du hovrar över diagrammet visas värdet vid den punkten.

Varje data punkt i diagrammet är en mängd data värden som tas emot i föregående samplings intervall eller granularitet. Granularitet visas överst på bladet och varierar med diagrammets övergripande skala.

Mått kan aggregeras på olika sätt:

* **Count** är antalet händelser som tagits emot i samplings intervallet. Den används för händelser som begär Anden. Variationer i diagrammets höjd anger variationer i den hastighet som händelserna inträffar. Men Observera att det numeriska värdet ändras när du ändrar samplings intervallet.
* **Sum** lägger till värdena för alla data punkter som tas emot över samplings intervallet eller perioden för diagrammet.
* **Medelvärdet** dividerar summan med antalet data punkter som tas emot under intervallet.
* **Unika** antal används för antal användare och konton. Under samplings intervallet, eller över tiden i diagrammet, visar bilden antalet olika användare som visas under den tiden.
* **%** -procent versioner av varje agg regering används endast med segmenterade diagram. Total summan lägger alltid till upp till 100% och diagrammet visar det relativa bidraget för olika komponenter i en total mängd.

    ![Procent agg regering](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Ändra sammansättnings typ

![Redigera diagrammet och välj sedan aggregation](./media/metrics-explorer/05-aggregation.png)

Standard metoden för varje mått visas när du skapar ett nytt diagram eller när alla mått är avmarkerade:

![Avmarkera alla mått om du vill se standardvärdena](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Fäst Y-axel 
Som standard visar ett diagram Y-axelns värden som börjar från noll till högsta värden i data området, för att ge en visuell representation av Quantum för värdena. Men i vissa fall är mer än Quantum det kan vara intressant att visuellt inspektera mindre ändringar i värden. För anpassningar som detta använder du redigerings funktionen i Y-axeln för att fästa det lägsta eller högsta värdet för Y-axeln på önskad plats.
Klicka på kryss rutan Avancerade inställningar för att ta fram Y-axelns intervall inställningar

![Klicka på avancerade inställningar, Välj anpassat intervall och ange minsta Max värde](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrera dina data
Om du bara vill se mått för en vald uppsättning egenskaps värden:

![Klicka på filter, expandera en egenskap och kontrol lera några värden](./media/metrics-explorer/19-filter.png)

Om du inte väljer några värden för en viss egenskap är det samma som att markera alla: det finns inget filter för den egenskapen.

Observera antalet händelser tillsammans med varje egenskaps värde. När du väljer värden för en egenskap justeras antalet bredvid andra egenskaps värden.

Filter gäller för alla diagram på ett blad. Om du vill att olika filter ska tillämpas på olika diagram skapar du och sparar olika mått blad. Om du vill kan du fästa diagram från olika blad på instrument panelen, så att du kan se dem bredvid varandra.

### <a name="remove-bot-and-web-test-traffic"></a>Ta bort robot-och webb test trafik
Använd filtrets **verkliga eller syntetiska trafik** och kontrol lera **verklig**.

Du kan också filtrera efter **källa för syntetisk trafik**.

### <a name="to-add-properties-to-the-filter-list"></a>Lägga till egenskaper i filter listan
Vill du filtrera telemetri efter en kategori som du väljer? Till exempel kanske du delar upp dina användare i olika kategorier, och du vill segmentera dina data efter dessa kategorier.

[Skapa din egen egenskap](../../azure-monitor/app/api-custom-events-metrics.md#properties). Ange den i en [telemetri initierare](../../azure-monitor/app/api-custom-events-metrics.md#defaults) så att den visas i all telemetri, inklusive standard-telemetri som skickas av olika SDK-moduler.

## <a name="edit-the-chart-type"></a>Redigera diagram typen
Observera att du kan växla mellan rutnät och diagram:

![Välj ett rutnät eller diagram och välj sedan en diagram typ](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Spara mått bladet
När du har skapat några diagram sparar du dem som favorit. Du kan välja om du vill dela den med andra grupp medlemmar, om du använder ett organisations konto.

![Välj favorit](./media/metrics-explorer/21-favorite-save.png)

Om du vill se bladet igen **går du till översikts bladet** och öppnar favoriter:

![I översikts bladet väljer du favoriter](./media/metrics-explorer/22-favorite-get.png)

Om du har valt relativt tidsintervallet när du sparade kommer bladet att uppdateras med de senaste måtten. Om du väljer absolut tidsintervall visas samma data varje gång.

## <a name="reset-the-blade"></a>Återställa bladet
Om du redigerar ett blad, men sedan vill gå tillbaka till den ursprungliga sparade uppsättningen, klickar du bara på Återställ.

![I knapparna överst i Metric Explorer](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live Metrics-dataström

Om du vill visa en mycket mer omedelbar vy över din telemetri öppnar du [Live Stream](live-stream.md). De flesta mått tar några minuter innan de visas, på grund av agg regerings processen. Som kontrast är Live-måtten optimerade för låg latens. 

## <a name="set-alerts"></a>Ange aviseringar
För att få ett meddelande via e-post om ovanliga värden för alla mått, Lägg till en avisering. Du kan antingen välja att skicka e-postmeddelandet till konto administratörer eller till vissa e-postadresser.

![I Metrics Explorer väljer du aviserings regler, Lägg till avisering](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Lär dig mer om aviseringar][alerts].


## <a name="continuous-export"></a>Kontinuerlig export
Om du vill att data ska exporteras kontinuerligt så att du kan bearbeta dem externt bör du överväga att använda [kontinuerlig export](../../azure-monitor/app/export-telemetry.md).

### <a name="power-bi"></a>Power BI
Om du vill ha ännu bättre vyer av dina data kan du [Exportera till Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analyser
[Analytics](../../azure-monitor/app/analytics.md) är ett mer flexibelt sätt att analysera din telemetri med ett kraftfullt frågespråk. Använd den om du vill kombinera eller beräkna resultat från mått eller utföra en djupgående utforskning av appens senaste prestanda. 

I ett mått diagram kan du klicka på analys ikonen för att komma direkt till motsvarande Analytics-fråga.

## <a name="troubleshooting"></a>Felsöka
*Jag ser inga data i diagrammet.*

* Filter gäller för alla diagram på bladet. Se till att du inte har angett något filter som utesluter alla data på ett annat, medan du fokuserar på ett diagram.

    Om du vill ange olika filter för olika diagram skapar du dem på olika blad, sparar dem som separata favoriter. Om du vill kan du fästa dem på instrument panelen så att du kan se dem bredvid varandra.
* Om du grupperar ett diagram efter en egenskap som inte har definierats för måttet, kommer det inte att finnas något i diagrammet. Försök rensa Group by eller Välj en annan grupp egenskap.
* Prestanda data (CPU, IO-taxa och så vidare) är tillgängliga för Java-webbtjänster, Windows-skrivbordsappar, [IIS-webbappar och-tjänster om du installerar status övervakaren](../../azure-monitor/app/monitor-performance-live-website-now.md)och [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). Den är inte tillgänglig för Azure Websites.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
* [Övervaka användning med Application Insights](../../azure-monitor/app/usage-overview.md)
* [Använda diagnostisk sökning](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
