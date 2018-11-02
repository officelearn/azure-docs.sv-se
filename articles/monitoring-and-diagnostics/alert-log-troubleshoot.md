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
ms.openlocfilehash: 0e1cb2cdd6270590def11479cc5859d996d84caa
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50749057"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Felsökning av aviseringar i Azure Monitor  

## <a name="overview"></a>Översikt
Den här artikeln visar du hantera vanliga problem som kan uppstå när du konfigurerar aviseringar i Azure monitor. Och ge lösning på vanliga frågor om funktioner eller konfigurationen av aviseringar. Termen **Loggaviseringar** att beskriva aviseringar där signalen är anpassad fråga baserat på [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Application Insights](../application-insights/app-insights-analytics.md). Mer information om funktioner, terminologi och typer från [Loggaviseringar - översikt](monitor-alerts-unified-log.md).

> [!NOTE]
> Den här artikeln tar inte hänsyn fall när regeln visas som utlöses i Azure portal och avisering via associerade åtgärd grupperna. Sådana fall finns information i artikeln på [åtgärdsgrupper](monitoring-action-groups.md).


## <a name="log-alert-didnt-fire"></a>Log aviseringen utlöses inte

Detaljerad nästa är några vanliga orsaker till varför en konfigurerade [loggvarningsregel i Azure Monitor](alert-log.md) inte hämta utlöses när den visas i [Azure Alerts](monitoring-alerts-managing-alert-states.md)när kommer att vara aktiverade. 

### <a name="data-ingestion-time-for-logs"></a>Tid för inmatning av data för loggar
Logga avisering fungerar genom att regelbundet köra kunden tillhandahåller fråga baserat på [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Application Insights](../application-insights/app-insights-analytics.md). Båda drivs av kraften i Analytics, som bearbetar stora mängder loggdata och tillhandahåller funktioner på samma. Som Log Analytics-tjänsten innebär bearbetning av många terabyte data från tusentals kunder och från olika källor i hela världen – tjänsten är sårbara för tidsfördröjning. Mer information finns i [tid för inmatning av Data i Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Att övervinna datainmatning fördröjningen som kan uppstå i Log Analytics och Application Insights-loggar. log aviseringen ska vänta och försök igen om en stund när den hittar data inte matas ännu för aviseringar tidsperioden. Aviseringar har set på en ökar exponentiellt wait-tid för att se till att vi vänta tid som krävs för de data som matas in av Log Analytics. Därför om loggarna med din loggvarningsregel påverkas av inmatning fördröjningar, utlöser sedan log avisering bara när data är tillgängliga i Log Analytics efter inmatning och när lucka i exponentiell tiden på grund av loggen alert-tjänsten försöker flera gånger under tiden .

### <a name="incorrect-time-period-configured"></a>Felaktig tidsperiod konfigurerad
Enligt beskrivningen i artikeln på [terminologi för loggaviseringar](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types), tidsperioden som anges i konfigurationen anger tidsintervall för frågan. Frågan returnerar bara de poster som har skapats i det här intervallet tid. Tidsperiod begränsar de data som hämtats för loggfråga att förhindra missbruk och kringgår alla tid-kommandon (t.ex. sedan) används i loggen frågan. 
*Till exempel om hur lång tid har angetts till 60 minuter och frågan körs klockan 13:15, returneras endast de poster som har skapats mellan 12:15 PM och 1:15 log frågan. Om log frågan använder tid kommandot som sedan nu 1d, log frågan skulle köras endast för data mellan 12:15 PM och 1:15 – som om det finns data för endast de senaste 60 minuterna. Och inte för sju dagarnas data som anges i loggfråga.*

Baserat på din fråga logik, kontrollera om lämplig tidsperiod i konfigurationen har angetts. Till exempel som vi nämnt tidigare, om loggen fråga använder sedan (1d) som visas med grön markör - och sedan hur lång tid ska ställas in till 24 timmar eller 1 440 minuter (som visas i rött), för att säkerställa att den angivna frågan körs korrekt enligt.
    ![Tidsperiod](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Visa inga aviseringar alternativet har ställts in
Enligt beskrivningen i steg 8 i artikeln på [skapar en loggvarningsregel i Azure-portalen](alert-log.md#managing-log-alerts-from-the-azure-portal), log avisering innehåller ett alternativ för Konfigurera automatisk Undertryckning av regeln och förhindra meddelande/utlösare för stipulerade lång tid. Utelämna aviseringar alternativet medför log avisering ska köras när inte utlösa åtgärdsgrupp för den tid som anges i **Visa inga aviseringar** alternativet och kan därför användare kan känna till att den här aviseringen inte utlöses när i stället det hindrades som konfigurerats .
    ![Visa inga aviseringar](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Varningsregel för metriska måttenheter är felaktig
Metrisk måttenhet typen av loggvarningsregler är undertyp till aviseringar, som har särskilda funktioner men som i sin tur använder avisering frågesyntaxen-begränsningar. Metrisk måttenhet log avisering-regeln kräver utdata från aviseringsfråga att tillhandahålla en metrisk tidsserie - beräknas för en tabell med olika lika stora tidsperioder tillsammans med motsvarande värden för AggregatedValue. Dessutom kan användare välja att ha i ytterligare tabellvariabler tillsammans med AggregatedValue dator, Node, t.ex. med vilken data i tabellen kan sorteras.

Anta exempelvis att metriska måttenheter loggvarningsregel har konfigurerats som:
- frågan var: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- i 6 timmar
- tröskelvärdet på 50
- aviseringslogik tre på varandra följande överträdelser
- Sammanställd vid som $table

Nu eftersom vi har använt i kommandot sammanfatta... genom att och två variabler som tillhandahålls: tidsstämpel & $table; aviseringen som tjänst väljer $table för ”sammanställd vid” – i princip sortera resultattabellen efter fält: $table - enligt nedan och tittar på flera AggregatedValue för varje tabell Skriv (till exempel availabilityResults) för att se om det var efterföljande överträdelser av 3 eller mer.

   ![Metrisk mätning Frågekörningen med flera värden](./media/monitor-alerts-unified/LogMMQuery.png)

Som ”sammanställd” är $table – sorteras data på $table kolumn (som i rött); Vi gruppen och leta efter typer av fältet ”sammanställd vid” (det vill säga) $table – till exempel: värdena för availabilityResults betraktas som en rityta/entitet (som markerade i Orange). I det här värdet diagram/entitet – avisering tjänsten söker efter tre på varandra följande intrång inträffar (som visas i grönt) för vilka aviseringen ska hämta utlöst för tabellvärde ”availabilityResults”. På samma sätt, om för ett annat värde för $table om tre efterföljande överträdelser ses - en annan avisering utlöses för samma; med alert-tjänsten Sortera automatiskt värdena i en rityta/entitet (som i Orange) efter tid.

Anta nu, metriska måttenheter loggvarningsregler ändrades och frågan var `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` med resten av konfig kvar samma som innan inklusive aviseringslogiken för tre efterföljande överträdelser. Alternativet ”Aggregera på” i det här fallet kommer att som standard: tidsstämpel. Eftersom bara ett värde har angetts i frågan för summera... efter tidsstämpel (det vill säga); liknar tidigare exempel slutet av körningen utdata skulle vara som på bilden nedan. 

   ![Metrisk mätning Frågekörningen med rapportanvändare värde](./media/monitor-alerts-unified/LogMMtimestamp.png)

Som ”sammanställd” är timestamp – sorteras data på kolumn för tidsstämpel (som i rött); sedan vi Gruppera efter tidsstämpel – till exempel: värden för `2018-10-17T06:00:00Z` betraktas som en rityta/entitet (som markerade i Orange). I det här värdet diagram/entitet – hittar meddelandetjänst kommer aldrig hämta utlöst inga efterföljande överträdelser förekommer (som varje tidsstämpelvärde har bara en post) och kan därför avisering. Därför i detta fall är måste användaren antingen-
- Lägga till en dummy variabel eller en befintlig variabel (t.ex. $table) korrekt sorteringsmetoden klar med hjälp av ”sammansatt vid” fält som har konfigurerats
- (Eller) Konfigurera om varningsregel för att använda aviseringslogiken utifrån *Totalt antal överträdelser* i stället på rätt sätt
 
## <a name="log-alert-fired-unnecessarily"></a>Log avisering har utlösts onödigt
Detaljerad nästa är några vanliga orsaker till varför en konfigurerade [loggvarningsregel i Azure Monitor](alert-log.md) kan utlösas när den visas i [Azure Alerts](monitoring-alerts-managing-alert-states.md), när du inte den ska vara aktiverade.

### <a name="alert-triggered-by-partial-data"></a>Varningen aktiverades av partiella data
Är Log Analytics och Application Insights Analytics är föremål för inmatning fördröjningar och bearbetning. på grund av som, kan vid tidpunkten när angivna loggvarningsfråga körs – det finnas fall ingen information är tillgänglig eller bara vissa data som är tillgängliga. Mer information finns i [tid för inmatning av Data i Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Beroende på hur regeln har konfigurerats, det kan finnas argumentantal firing om det finns inga eller partiella data i loggar vid tidpunkten för avisering körning. I sådana fall är det bäst att aviseringsfråga eller konfigurationsversion ändras. *Till exempel om varningsregel för loggen är konfigurerad för att utlösa när antalet analytics-frågan är mindre än (säga) 5. sedan när det finns ingen data (ingen post) eller ofullständiga resultat (en post) ska varningsregeln få utlöses. Där-som efter inmatning fördröjning när körs samma fråga i Analytics frågan med fullständig data kan ge resultatet som 10 poster.*

### <a name="alert-query-output-misunderstood"></a>Aviseringsfråga utdata tror många
För loggaviseringar tillhandahålls logiken för avisering av användaren via analytics-fråga. Den angivna analytics-frågan kan använda olika Big Data och matematiska funktioner för att skapa specifika konstruktioner. Tjänsten aviseringar ska köra kunden tillhandahåller frågan med intervall som angetts med data för tidsperiod som angetts. avisering service gör vissa ändringar i fråga tillhandahålls, baserat på den aviseringstyp som valts och samma kan närvara i avsnittet ”fråga för att köras” konfigurera signalen logik på skärmen som på bilden nedan: ![fråga som ska köras](./media/monitor-alerts-unified/LogAlertPreview.png)
 
Vad som anges i **fråga som ska köras** avsnittet är vilka log avisering service körs; användare kan köra den angivna frågan samt timespan via [analysportalen](../log-analytics/log-analytics-log-search-portals.md) eller [API för textanalys](https://docs.microsoft.com/en-us/rest/api/loganalytics/) – Om de vill förstå innan du skapande av varning vilken aviseringsfråga utgående kan vara.
 
## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Loggaviseringar i Azure-aviseringar](monitor-alerts-unified-log.md)
* Läs mer om [Application Insights](../application-insights/app-insights-analytics.md)
* Läs mer om [Log Analytics](../log-analytics/log-analytics-overview.md). 