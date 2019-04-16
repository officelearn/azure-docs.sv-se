---
title: Felsökning av aviseringar i Azure Monitor | Microsoft Docs
description: Vanliga problem, fel och upplösning för loggvarningsregler i Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0c7189f1d43a114532b30b0c1aabe6f7cd4402d8
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578721"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Felsökning av aviseringar i Azure Monitor  

## <a name="overview"></a>Översikt

Den här artikeln visar hur du löser vanliga problem som kan uppstå när du konfigurerar aviseringar i Azure Monitor. Det ger också lösningar på vanliga frågor om funktioner eller konfigurationen av aviseringar. 

Termen **Loggaviseringar** beskriver aviseringar att fire baserat på en loggfråga i en [Log Analytics-arbetsyta](../learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/analytics.md). Mer information om funktioner, terminologi och typer i [Loggaviseringar - översikt](../platform/alerts-unified-log.md).

> [!NOTE]
> Den här artikeln tar inte hänsyn fall när Azure-portalen visar och varningen aktiverades av regeln och ett meddelande som utförs av en tillhörande åtgärd-grupperna. Sådana fall finns information i artikeln på [åtgärdsgrupper](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Log aviseringen utlöses inte

Här följer några vanliga orsaker till varför en konfigurerade [loggvarningsregel i Azure Monitor](../platform/alerts-log.md) tillstånd inte visar [som *utlösta* när förväntades](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Tid för inmatning av data för loggar

Log avisering körs regelbundet frågan baserat på [Log Analytics](../learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/analytics.md). Eftersom Azure Monitor bearbetar många terabyte med data från tusentals kunder från olika källor i hela världen, är tjänsten sårbara för en varierande tidsfördröjning. Mer information finns i [tid för inmatning av Data i Azure Monitor-loggar](../platform/data-ingestion-time.md).

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

Är Log Analytics och Application Insights Analytics är föremål för inmatning fördröjningar och bearbetning. på grund av som, kan vid tidpunkten när angivna loggvarningsfråga körs – det finnas fall ingen information är tillgänglig eller bara vissa data som är tillgängliga. Mer information finns i [logga tid för inmatning av data i Azure Monitor](../platform/data-ingestion-time.md).

Beroende på hur regeln har konfigurerats, det kan vara felaktigt firing om det finns inga eller partiella data i loggar vid tidpunkten för avisering körning. I sådana fall rekommenderar vi att du kan ändra aviseringsfråga eller konfiguration. 

Exempel: om loggvarningsregel har konfigurerats för att utlösa när antalet resultat från en analytics-fråga är mindre än 5, sedan aviseringen utlöses när det finns inga data (ingen post) eller ofullständiga resultat (en post). Efter fördröjning inmatning av data kan dock samma fråga med fullständig data ger ett resultat av 10 poster.

### <a name="alert-query-output-misunderstood"></a>Aviseringsfråga utdata tror många

Du kan ange logiken för loggaviseringar i en analytics-fråga. Analytics-fråga kan använda olika stordata och matematiska funktioner.  Aviseringar tjänsten kör din fråga med intervall som angetts med data för en angiven tidsperiod. Aviseringar service gör små ändringar i angivna frågan baserat på den aviseringstyp som valts. Den här ändringen kan visas i avsnittet ”fråga för att köras” i *konfigurera signallogiken* skärmen som visas nedan: ![Fråga som ska köras](media/alert-log-troubleshoot/LogAlertPreview.png)

Vad som visas i den **fråga som ska köras** rutan är varning Loggtjänsten körs. Du kan köra den angivna frågan samt timespan via [analysportalen](../log-query/portals.md) eller [API för textanalys](https://docs.microsoft.com/rest/api/loganalytics/) om du vill förstå vilka aviseringsfrågan utdata kan finnas innan du skapar aviseringen.

## <a name="log-alert-was-disabled"></a>Log-aviseringen har inaktiverats

Nedan visas några orsaker vilket [loggvarningsregel i Azure Monitor](../platform/alerts-log.md) kanske har inaktiverats av Azure Monitor.

### <a name="resource-on-which-alert-was-created-no-longer-exists"></a>Resursen där aviseringen skapades inte längre finns

Loggvarningsregler som skapas i Azure Monitor rikta en specifik resurs som en Azure Log Analytics-arbetsyta, Azure Application Insights-app och Azure-resurs. Och kör avisering Loggtjänsten sedan analytics-fråga som angetts i regeln för det angivna målet. Men när regeln skapades, ofta användare går du vidare till ta bort från Azure eller flytta i Azure - målet för aviseringsregeln. Eftersom målet för loggvarningsregel inte längre giltig, regelns går inte att köra.

I sådana fall kan Azure Monitor inaktivera aviseringen log och se till att kunder inte faktureras onödigt, när regeln själva inte kan kan köras kontinuerligt av ansenliga period som en vecka. Användare kan ta reda på den exakta tidpunkt då loggvarningsregel har inaktiverats av Azure Monitor via [Azure-aktivitetsloggen](../../azure-resource-manager/resource-group-audit.md). I Azure-aktivitetsloggen när loggvarningsregel inaktiveras av Azure, läggs en händelse till i Azure-aktivitetsloggen.

En exempelhändelse i aktivitetsloggen för Azure för varningsregeln inaktiveras på grund av dess kontinuerliga misslyckades; visas nedan.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-log-alert-is-not-valid"></a>Fråga som används i loggen aviseringen är inte giltig

Varje loggvarningsregel som skapats i Azure Monitor som en del av dess konfiguration måste ange en analytics-fråga som ska köras regelbundet av tjänsten avisering. Medan analytics-fråga kan ha rätt syntax vid tidpunkten för skapande eller uppdatering. Vissa gånger under en viss tidsperiod, frågan ger i loggen varningsregeln kan utveckla syntaxproblem och orsaka regelutförandet till börjar misslyckas. Några vanliga orsaker till varför analytics-fråga i en loggvarningsregel kan utveckla fel är:

- Fråga skrivs till [körs över flera resurser](../log-query/cross-workspace-query.md) och en eller flera av de resurser som anges, nu inte finns.
- Det har inga dataflöde för analytics-plattformen, vilket den [Frågekörningen ger fel](https://dev.loganalytics.io/documentation/Using-the-API/Errors) eftersom det finns inga data för den angivna frågan.
- Ändringar i [frågespråk](https://docs.microsoft.com/azure/kusto/query/) har inträffat under vilka kommandon och -funktioner har ett reviderade format. Tidigare tillhandahållna frågan i varningsregeln är därför inte längre giltig.

Användaren skall varnas om det här beteendet först [Azure Advisor](../../advisor/advisor-overview.md). En rekommendation skulle läggas till för den specifika loggvarningsregel på Azure Advisor under kategorin för hög tillgänglighet med medelstor inverkan och en beskrivning som ”reparation din loggvarningsregel för att säkerställa övervakning”. Om aviseringsfrågan i den angivna loggvarningsregel inte är ändras efter sju dagar av den givande rekommendationen om Azure Advisor. Azure Monitor kommer sedan inaktivera log aviseringen och se till kunder inte faktureras onödigt, när regeln själva inte kan kan köras kontinuerligt av ansenliga period som en vecka.

Användare kan ta reda på den exakta tidpunkt då loggvarningsregel har inaktiverats av Azure Monitor via [Azure-aktivitetsloggen](../../azure-resource-manager/resource-group-audit.md). I Azure-aktivitetsloggen, när loggvarningsregel inaktiveras av Azure - läggs en händelse till i Azure-aktivitetsloggen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Loggaviseringar i Azure-aviseringar](../platform/alerts-unified-log.md)
- Läs mer om [Application Insights](../../azure-monitor/app/analytics.md)
- Läs mer om [logga frågor](../log-query/log-query-overview.md)
