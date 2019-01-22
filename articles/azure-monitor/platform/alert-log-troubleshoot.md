---
title: Felsökning av aviseringar i Azure Monitor
description: Vanliga problem, fel och lösningar för logg aviseringsregler i Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 56d76cd43b63a389569ae39c1e987a5fccbb9793
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429454"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Felsökning av aviseringar i Azure Monitor  

## <a name="overview"></a>Översikt

Den här artikeln visar hur du löser vanliga problem som kan uppstå när du konfigurerar aviseringar i Azure monitor. Det ger också lösningar på vanliga frågor om funktioner eller konfigurationen av aviseringar. 

Termen **Loggaviseringar** beskriver aviseringar att fire baserat på en anpassad fråga i [Log Analytics](../learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/analytics.md). Mer information om funktioner, terminologi och typer i [Loggaviseringar - översikt](../platform/alerts-unified-log.md).

> [!NOTE]
> Den här artikeln tar inte hänsyn fall när Azure-portalen visar och varningen aktiverades av regeln och ett meddelande som utförs av en tillhörande åtgärd-grupperna. Sådana fall finns information i artikeln på [åtgärdsgrupper](../platform/action-groups.md).


## <a name="log-alert-didnt-fire"></a>Log aviseringen utlöses inte

Här följer några vanliga orsaker till varför en konfigurerade [loggvarningsregel i Azure Monitor](../platform/alerts-log.md) tillstånd inte visar [som *utlösta* när förväntades](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Tid för inmatning av data för loggar

Log avisering körs regelbundet frågan baserat på [Log Analytics](../learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/analytics.md). Eftersom Log Analytics bearbetar många terabyte med data från tusentals kunder från olika källor i hela världen, är tjänsten sårbara för en varierande tidsfördröjning. Mer information finns i [Data ingestion time in Log Analytics](../platform/data-ingestion-time.md) (Tid för datainmatning i Log Analytics).

För att minimera fördröjning för inmatning av data, systemet väntar och försöker aviseringsfrågan flera gånger om den hittar nödvändiga data inte matas ännu. Systemet har ett ökar exponentiellt väntetid som angetts. Vilka log aviseringar endast utlösare när data är tillgängliga så att de fördröjning kan bero på långsam logg för datainmatning. 

### <a name="incorrect-time-period-configured"></a>Felaktig tidsperiod konfigurerad

Enligt beskrivningen i artikeln på [terminologi för loggaviseringar](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)tidpunkten period angiven i konfigurationen anger tidsintervall för frågan. Frågan returnerar bara de poster som har skapats i det här intervallet tid. Tidsperiod begränsar de data som hämtats för loggfråga att förhindra missbruk och kringgår alla tid-kommandon (t.ex. *sedan*) används i loggen frågan. Till exempel om hur lång tid har angetts till 60 minuter och frågan körs klockan 13:15, används bara de poster som har skapats mellan 12:15 PM och 1:15 för log-frågan. Om loggfråga använder ett tid-kommando som *sedan (1d)*, frågan används fortfarande bara data mellan 12:15 PM och 1:15 eftersom hur lång tid är inställd på att interval.*

Därför matchar Kontrollera den aktuella tidsperioden i konfigurationen din fråga. Till exempel som vi nämnt tidigare, om log frågan använder *sedan (1d)* enligt med grön markör sedan hur lång tid bör anges till 24 timmar eller 1 440 minuter (som anges i rött), så utförs av frågan som avsett.

![Tidsperiod](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Visa inga aviseringar alternativet har ställts in

Enligt beskrivningen i steg 8 i artikeln på [skapar en loggvarningsregel i Azure-portalen](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), loggvarningar ger en **Visa inga aviseringar** alternativet för att utelämna utlösa och notification-åtgärder för en angiven tid. Därför kan du föreställa dig inte har en avisering utlöses när den gjorde i stället men hindrades.  

![Visa inga aviseringar](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Varningsregel för metriska måttenheter är felaktig

**Metrisk måttenhet loggvarningar** är en undertyp till aviseringar, som har särskilda funktioner och en begränsad avisering frågesyntax. En metrisk måttenhet loggvarningsregel kräver frågan utdata ska vara en metrisk tidsserie; det vill säga stora en tabell med olika lika tidsperioder tillsammans med motsvarande sammanställda värden. Dessutom kan användare välja att ha ytterligare variabler i tabellen tillsammans med AggregatedValue. Dessa variabler kan användas för att sortera tabellen. 

Anta exempelvis att en varningsregel för metriska måttenheter-loggen har konfigurerats som:

- frågan var: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- i 6 timmar
- tröskelvärdet på 50
- aviseringslogik tre på varandra följande överträdelser
- Sammanställd vid som $table

Eftersom kommandot innefattar *... summera efter* och tillhandahålls två variabler (tidsstämpel & $table), väljs automatiskt $table till *sammanställd vid*. Sorteras efter fältet resultattabellen *$table* enligt nedan och tittar sedan på flera AggregatedValue för varje tabelltyp (till exempel availabilityResults) för att se om det var efterföljande överträdelser av 3 eller mer.

![Metrisk mätning Frågekörningen med flera värden](media/alert-log-troubleshoot/LogMMQuery.png)

Som *sammanställd vid* har definierats för *$table*, data sorteras på $table kolumn (som i rött); sedan vi gruppen och leta efter typer av *sammanställd vid* fält (det vill säga) $table för Exempel: värdena för availabilityResults betraktas som en rityta/entitet (som markerade i Orange). I det här värdet diagram/entitet avisering tjänsten söker efter tre på varandra följande intrång inträffar (som visas i grönt) för vilka aviseringen ska hämta utlöst för tabellvärde ”availabilityResults”. På samma sätt, om för ett annat värde för $table om tre efterföljande överträdelser ses – en annan avisering om utlöses för samma sak; med alert-tjänsten Sortera automatiskt värdena i en rityta/entitet (som i Orange) efter tid.

Anta nu, metriska måttenheter loggvarningsregler ändrades och frågan var `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` med resten av konfig kvar samma som innan inklusive aviseringslogiken för tre efterföljande överträdelser. Alternativet ”Aggregera på” i det här fallet kommer att som standard: tidsstämpel. Eftersom bara ett värde har angetts i frågan för *... summera efter* (det vill säga) *tidsstämpel*; liknar tidigare exempel slutet av körningen resultatet skulle se ut som på bilden nedan.

   ![Metrisk mätning Frågekörningen med rapportanvändare värde](media/alert-log-troubleshoot/LogMMtimestamp.png)

Som *sammanställd vid* definieras på tidsstämpel, data sorteras på *tidsstämpel* kolumn (som i rött); sedan vi Gruppera efter tidsstämpel till exempel: värden för `2018-10-17T06:00:00Z` betraktas som en rityta/entitet (som markerat med Orange). I det här värdet diagram/entitet hittar aviseringstjänsten kommer aldrig hämta utlöst inga efterföljande överträdelser förekommer (som varje tidsstämpelvärde har bara en post) och kan därför avisering. Därför i detta fall är måste användaren antingen:

- Lägga till en dummy variabel eller en befintlig variabel (t.ex. $table) korrekt sorteringsmetoden klar med hjälp av ”sammansatt vid” fält som har konfigurerats
- (Eller) Konfigurera om varningsregel för att använda aviseringslogiken utifrån *Totalt antal överträdelser* i stället på rätt sätt

## <a name="log-alert-fired-unnecessarily"></a>Log avisering har utlösts onödigt

Detaljerad nästa är några vanliga orsaker till varför en konfigurerade [loggvarningsregel i Azure Monitor](../platform/alerts-log.md) kan utlösas när den visas i [Azure Alerts](../platform/alerts-managing-alert-states.md), när du inte den ska vara aktiverade.

### <a name="alert-triggered-by-partial-data"></a>Varningen aktiverades av partiella data

Är Log Analytics och Application Insights Analytics är föremål för inmatning fördröjningar och bearbetning. på grund av som, kan vid tidpunkten när angivna loggvarningsfråga körs – det finnas fall ingen information är tillgänglig eller bara vissa data som är tillgängliga. Mer information finns i [Data ingestion time in Log Analytics](../platform/data-ingestion-time.md) (Tid för datainmatning i Log Analytics).

Beroende på hur regeln har konfigurerats, det kan vara felaktigt firing om det finns inga eller partiella data i loggar vid tidpunkten för avisering körning. I sådana fall rekommenderar vi att du kan ändra aviseringsfråga eller konfiguration. 

Exempel: om loggvarningsregel har konfigurerats för att utlösa när antalet resultat från en analytics-fråga är mindre än 5, sedan aviseringen utlöses när det finns inga data (ingen post) eller ofullständiga resultat (en post). Efter fördröjning inmatning av data kan dock samma fråga med fullständig data ger ett resultat av 10 poster.

### <a name="alert-query-output-misunderstood"></a>Aviseringsfråga utdata tror många

Du kan ange logiken för loggaviseringar i en analytics-fråga. Analytics-fråga kan använda olika stordata och matematiska funktioner.  Aviseringar tjänsten kör din fråga med intervall som angetts med data för en angiven tidsperiod. Aviseringar service gör små ändringar i angivna frågan baserat på den aviseringstyp som valts. Detta syns i avsnittet ”fråga för att köras” i *konfigurera signallogiken* skärmen som visas nedan: ![Fråga som ska köras](media/alert-log-troubleshoot/LogAlertPreview.png)

Vad som visas i den **fråga som ska köras** rutan är varning Loggtjänsten körs. Du kan köra den angivna frågan samt timespan via [analysportalen](../log-query/portals.md) eller [API för textanalys](https://docs.microsoft.com/rest/api/loganalytics/) om du vill förstå vilka aviseringsfrågan utdata kan finnas innan du skapar aviseringen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Loggaviseringar i Azure-aviseringar](../platform/alerts-unified-log.md)
- Läs mer om [Application Insights](../../azure-monitor/app/analytics.md)
- Läs mer om [Log Analytics](../../log-analytics/log-analytics-overview.md)
