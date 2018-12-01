---
title: Felsökning av aviseringar i Azure Monitor
description: Vanliga problem, fel och lösningar för logg aviseringsregler i Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 0612a7798d3cc2e43efc296bd2b749735e74f765
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720855"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Felsökning av aviseringar i Azure Monitor  
## <a name="overview"></a>Översikt
Den här artikeln visar hur du löser vanliga problem som kan uppstå när du konfigurerar aviseringar i Azure monitor. Det ger också lösningar på vanliga frågor om funktioner eller konfigurationen av aviseringar. 

Termen **Loggaviseringar** beskriver aviseringar att fire baserat på en anpassad fråga i [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Application Insights](../application-insights/app-insights-analytics.md). Mer information om funktioner, terminologi och typer i [Loggaviseringar - översikt](monitor-alerts-unified-log.md).

> [!NOTE]
> Den här artikeln tar inte hänsyn fall när Azure-portalen visar och varningen aktiverades av regeln och ett meddelande som utförs av en tillhörande åtgärd-grupperna. Sådana fall finns information i artikeln på [åtgärdsgrupper](monitoring-action-groups.md).


## <a name="log-alert-didnt-fire"></a>Log aviseringen utlöses inte

Här följer några vanliga orsaker till varför en konfigurerade [loggvarningsregel i Azure Monitor](alert-log.md) tillstånd inte visar [som *utlösta* när förväntades](monitoring-alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Tid för inmatning av data för loggar
Log avisering körs regelbundet frågan baserat på [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Application Insights](../application-insights/app-insights-analytics.md). Eftersom Log Analytics bearbetar många terabyte med data från tusentals kunder från olika källor i hela världen, är tjänsten sårbara för en varierande tidsfördröjning. Mer information finns i [tid för inmatning av Data i Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

För att minimera fördröjning för inmatning av data, systemet väntar och försöker aviseringsfrågan flera gånger om den hittar nödvändiga data inte matas ännu. Systemet har ett ökar exponentiellt väntetid som angetts. Vilka log aviseringar endast utlösare när data är tillgängliga så att de fördröjning kan bero på långsam logg för datainmatning. 

### <a name="incorrect-time-period-configured"></a>Felaktig tidsperiod konfigurerad
Enligt beskrivningen i artikeln på [terminologi för loggaviseringar](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types)tidpunkten period angiven i konfigurationen anger tidsintervall för frågan. Frågan returnerar bara de poster som har skapats i det här intervallet tid. Tidsperiod begränsar de data som hämtats för loggfråga att förhindra missbruk och kringgår alla tid-kommandon (t.ex. *sedan*) används i loggen frågan. Till exempel om hur lång tid har angetts till 60 minuter och frågan körs klockan 13:15, används bara de poster som har skapats mellan 12:15 PM och 1:15 för log-frågan. Om loggfråga använder ett tid-kommando som *sedan (1d)*, frågan används fortfarande bara data mellan 12:15 PM och 1:15 eftersom hur lång tid är inställd på att interval.*

Därför matchar Kontrollera den aktuella tidsperioden i konfigurationen din fråga. Till exempel som vi nämnt tidigare, om log frågan använder *sedan (1d)* enligt med grön markör sedan hur lång tid bör anges till 24 timmar eller 1 440 minuter (som anges i rött), så utförs av frågan som avsett.

![Tidsperiod](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Visa inga aviseringar alternativet har ställts in
Enligt beskrivningen i steg 8 i artikeln på [skapar en loggvarningsregel i Azure-portalen](alert-log.md#managing-log-alerts-from-the-azure-portal), loggvarningar ger en **Visa inga aviseringar** alternativet för att utelämna utlösa och notification-åtgärder för en angiven tid. Därför kan du föreställa dig inte har en avisering utlöses när den gjorde i stället men hindrades.  

![Visa inga aviseringar](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Varningsregel för metriska måttenheter är felaktig
**Metrisk måttenhet loggvarningar** är en undertyp till aviseringar, som har särskilda funktioner och en begränsad avisering frågesyntax. En metrisk måttenhet loggvarningsregel kräver frågan utdata ska vara en metrisk tidsserie; det vill säga stora en tabell med olika lika tidsperioder tillsammans med motsvarande sammanställda värden. Dessutom kan användare välja att ha ytterligare variabler i tabellen tillsammans med AggregatedValue. Dessa variabler kan användas för att sortera tabellen. 

Anta exempelvis att en varningsregel för metriska måttenheter-loggen har konfigurerats som:
- frågan var: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- i 6 timmar
- tröskelvärdet på 50
- aviseringslogik tre på varandra följande överträdelser
- Sammanställd vid som $table

Eftersom kommandot innefattar *... summera efter* och två variabler (tidsstämpel & $table), som tillhandahålls $table till ”sammanställd vid” väljs automatiskt. Sorteras efter fältet resultattabellen *$table* enligt nedan och tittar sedan på flera AggregatedValue för varje tabelltyp (till exempel availabilityResults) för att se om det var efterföljande överträdelser av 3 eller mer.

![Metrisk mätning Frågekörningen med flera värden](./media/monitor-alerts-unified/LogMMQuery.png)

Som ”sammanställd” är $table – sorteras data på $table kolumn (som i rött); Vi gruppen och leta efter typer av fältet ”sammanställd vid” (det vill säga) $table – till exempel: värdena för availabilityResults betraktas som en rityta/entitet (som markerade i Orange). I det här värdet diagram/entitet – avisering tjänsten söker efter tre på varandra följande intrång inträffar (som visas i grönt) för vilka aviseringen ska hämta utlöst för tabellvärde ”availabilityResults”. På samma sätt, om för ett annat värde för $table om tre efterföljande överträdelser ses – en annan avisering om utlöses för samma sak; med alert-tjänsten Sortera automatiskt värdena i en rityta/entitet (som i Orange) efter tid.

Anta nu, metriska måttenheter loggvarningsregler ändrades och frågan var `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` med resten av konfig kvar samma som innan inklusive aviseringslogiken för tre efterföljande överträdelser. Alternativet ”Aggregera på” i det här fallet kommer att som standard: tidsstämpel. Eftersom bara ett värde har angetts i frågan för summera... efter tidsstämpel (det vill säga); liknar tidigare exempel slutet av körningen utdata skulle vara som på bilden nedan. 

   ![Metrisk mätning Frågekörningen med rapportanvändare värde](./media/monitor-alerts-unified/LogMMtimestamp.png)

Som ”sammanställd” är timestamp – sorteras data på kolumn för tidsstämpel (som i rött); sedan vi Gruppera efter tidsstämpel – till exempel: värden för `2018-10-17T06:00:00Z` betraktas som en rityta/entitet (som markerade i Orange). I det här värdet diagram/entitet – hittar meddelandetjänst kommer aldrig hämta utlöst inga efterföljande överträdelser förekommer (som varje tidsstämpelvärde har bara en post) och kan därför avisering. Därför i detta fall är måste användaren antingen-
- Lägga till en dummy variabel eller en befintlig variabel (t.ex. $table) korrekt sorteringsmetoden klar med hjälp av ”sammansatt vid” fält som har konfigurerats
- (Eller) Konfigurera om varningsregel för att använda aviseringslogiken utifrån *Totalt antal överträdelser* i stället på rätt sätt
 
## <a name="log-alert-fired-unnecessarily"></a>Log avisering har utlösts onödigt
Detaljerad nästa är några vanliga orsaker till varför en konfigurerade [loggvarningsregel i Azure Monitor](alert-log.md) kan utlösas när den visas i [Azure Alerts](monitoring-alerts-managing-alert-states.md), när du inte den ska vara aktiverade.

### <a name="alert-triggered-by-partial-data"></a>Varningen aktiverades av partiella data
Är Log Analytics och Application Insights Analytics är föremål för inmatning fördröjningar och bearbetning. på grund av som, kan vid tidpunkten när angivna loggvarningsfråga körs – det finnas fall ingen information är tillgänglig eller bara vissa data som är tillgängliga. Mer information finns i [tid för inmatning av Data i Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Beroende på hur regeln har konfigurerats, det kan vara felaktigt firing om det finns inga eller partiella data i loggar vid tidpunkten för avisering körning. I sådana fall rekommenderar vi att du kan ändra aviseringsfråga eller konfiguration. 

Exempel: om loggvarningsregel har konfigurerats för att utlösa när antalet resultat från en analytics-fråga är mindre än 5, sedan aviseringen utlöses när det finns inga data (ingen post) eller ofullständiga resultat (en post). Efter fördröjning inmatning av data kan dock samma fråga med fullständig data ger ett resultat av 10 poster.

### <a name="alert-query-output-misunderstood"></a>Aviseringsfråga utdata tror många
Du kan ange logiken för loggaviseringar i en analytics-fråga. Analytics-fråga kan använda olika stordata och matematiska funktioner.  Aviseringar tjänsten kör din fråga med intervall som angetts med data för en angiven tidsperiod. Aviseringar service gör små ändringar i angivna frågan baserat på den aviseringstyp som valts. Detta syns i avsnittet ”fråga för att köras” i *konfigurera signallogiken* skärmen som visas nedan: ![fråga som ska köras](./media/monitor-alerts-unified/LogAlertPreview.png)
 
Vad som visas i den **fråga som ska köras** rutan är varning Loggtjänsten körs. Du kan köra den angivna frågan samt timespan via [analysportalen](../log-analytics/log-analytics-log-search-portals.md) eller [API för textanalys](https://docs.microsoft.com/rest/api/loganalytics/) om du vill förstå vilka aviseringsfrågan utdata kan finnas innan du skapar aviseringen.
 
## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Loggaviseringar i Azure-aviseringar](monitor-alerts-unified-log.md)
* Läs mer om [Application Insights](../application-insights/app-insights-analytics.md)
* Läs mer om [Log Analytics](../log-analytics/log-analytics-overview.md). 

