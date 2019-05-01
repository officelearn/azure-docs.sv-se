---
title: Felsöka loggaviseringar i Azure Monitor | Microsoft Docs
description: Vanliga problem, fel och lösningar för loggvarningsregler i Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c7712fc2ce55a3d22995bb119a9ee485a064903
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683397"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Felsöka loggaviseringar i Azure Monitor  

Den här artikeln visar hur du löser vanliga problem som kan uppstå när du konfigurerar aviseringar i Azure Monitor. Det ger också lösningar på vanliga problem med funktioner och konfigurationen av aviseringar. 

Termen *loggaviseringar* beskriver aviseringar att fire baserat på en loggfråga i en [Azure Log Analytics-arbetsyta](../learn/tutorial-viewdata.md) eller i [Azure Application Insights](../../azure-monitor/app/analytics.md). Mer information om funktioner, terminologi och typer i [Loggaviseringar i Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Den här artikeln tar inte hänsyn om Azure-portalen visar en varningsregel som utlöses och ett meddelande inte har utförts av en tillhörande åtgärdsgrupp. Finns i informationen i sådana fall kan [skapa och hantera åtgärdsgrupper i Azure-portalen](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Log aviseringen utlöses inte

Här följer några vanliga orsaker till varför tillståndet för en konfigurerad [loggvarningsregel i Azure Monitor](../platform/alerts-log.md) inte visar [som *utlösta* när förväntades](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Tid för inmatning av data för loggar

En avisering om log körs regelbundet frågan baserat på [Log Analytics](../learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/analytics.md). Eftersom Azure Monitor bearbetar många terabyte med data från tusentals kunder från olika källor i hela världen, är tjänsten sårbara för olika tidsfördröjning. Mer information finns i [tid för inmatning av Data i Azure Monitor-loggar](../platform/data-ingestion-time.md).

För att undvika förseningar systemet väntar och försöker aviseringsfrågan flera gånger om den hittar nödvändiga data inte matas ännu. Systemet har ett ökar exponentiellt väntetid som angetts. Log-aviseringen utlöses endast efter att data är tillgängliga, så fördröjningen kan vara på grund av långsam inmatning av loggdata. 

### <a name="incorrect-time-period-configured"></a>Felaktig tidsperiod konfigurerad

Enligt beskrivningen i artikeln på [terminologi för loggaviseringar](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), hur lång tid som anges i konfigurationen anger tidsintervall för frågan. Frågan returnerar bara de poster som har skapats i det här intervallet. 

Hur lång tid begränsar data hämtas för en loggfråga att förhindra missbruk och den kringgår alla tid-kommandon (t.ex. **sedan**) används i en loggfråga. Till exempel om hur lång tid har angetts till 60 minuter och frågan körs klockan 13:15, används bara de poster som har skapats mellan 12:15 PM och 1:15 för log-frågan. Om loggfråga använder ett tid-kommando som **sedan (1d)**, endast använder data mellan 12:15 PM och 1:15 i frågan eftersom tidsperioden är inställt på det här intervallet.

Kontrollera att tidsperioden i konfigurationen matchar din fråga. Till exempel som visades tidigare, om log frågan använder **sedan (1d)** med grön markör hur lång tid ska vara inställd på 24 timmar och 1 440 minuter (visas i rött). Den här inställningen garanterar att frågan ska köras som avsett.

![Tidsperiod](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Visa inga aviseringar alternativet har ställts in

Enligt beskrivningen i steg 8 i artikeln på [skapar en loggvarningsregel i Azure-portalen](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), loggvarningar ger en **Visa inga aviseringar** alternativet för att utelämna utlösa och notification-åtgärder för en angiven tid. Därför kanske du tror att en avisering inte utlöses. I själva verket utlöses men hindrades.  

![Visa inga aviseringar](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Varningsregel för metriska måttenheter är felaktig

*Metrisk måttenhet loggvarningar* är en undertyp till aviseringar som har särskilda funktioner och en begränsad avisering frågesyntax. En regel för en metrisk måttenhet log-avisering kräver frågan utdata ska vara en metrisk tidsserie. Utdata är en tabell med olika, lika stora tidsperioder tillsammans med motsvarande sammanställda värden. 

Du kan välja att ha ytterligare variabler i tabellen tillsammans med **AggregatedValue**. Dessa variabler kan användas för att sortera tabellen. 

Anta exempelvis att en regel för en avisering för metriska måttenheter log har konfigurerats som:

- Fråga med `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- i 6 timmar
- tröskelvärdet på 50
- aviseringslogik tre på varandra följande överträdelser
- **Sammanställd vid** som **$table**

Eftersom kommandot innefattar **sammanfatta... genom att** och tillhandahåller två variabler (**tidsstämpel** och **$table**), väljs automatiskt **$table** för **sammanställd vid** . Sorterar systemet resultattabellen genom den **$table** fältet som du ser i följande skärmbild. Och sedan den tittar på multipeln **AggregatedValue** instanser för varje tabelltyp (t.ex. **availabilityResults**) att se om det fanns tre eller fler efterföljande överträdelser.

![Metrisk måttenhet Frågekörningen med flera värden](media/alert-log-troubleshoot/LogMMQuery.png)

Eftersom **sammanställd vid** har definierats för **$table**, data sorteras i en **$table** kolumnen (visas i rött). Vi gruppen och leta efter typer av den **sammanställd vid** fält. 

Till exempel för **$table**, värden för **availabilityResults** betraktas som en rityta/entitet (visas i orange). I det här värdet diagram/entitet kontrollerar tjänsten avisering tre efterföljande överträdelser (visas i grönt). Överträdelserna utlösa en avisering för tabellvärde **availabilityResults**. 

På samma sätt, om tre efterföljande överträdelser bero på ett annat värde för **$table**, en annan avisering utlöses för samma sak. Tjänsten avisering sorteras värdena i en rityta/entitet (visas i orange) automatiskt efter tid.

Anta nu att regeln för metriska måttenheter log aviseringen har ändrats och frågan var `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. Resten av konfigurationen densamma som tidigare, inklusive aviseringslogiken för tre efterföljande överträdelser. Den **sammanställd vid** alternativet i det här fallet är **tidsstämpel** som standard. Endast ett värde har angetts i frågan för **sammanfatta... genom att** (det vill säga **tidsstämpel**). Utdata till slutet av körningen vore som det tidigare exemplet, såsom illustreras på följande sätt.

   ![Metrisk måttenhet Frågekörningen med rapportanvändare värde](media/alert-log-troubleshoot/LogMMtimestamp.png)

Eftersom **sammanställd vid** har definierats för **tidsstämpel**, data sorteras på den **tidsstämpel** kolumnen (visas i rött). Sedan vi Gruppera efter **tidsstämpel**. Till exempel värden för `2018-10-17T06:00:00Z` betraktas som en rityta/entitet (visas i orange). I det här värdet diagram/entitet, tjänsten avisering hittar inga efterföljande överträdelser (eftersom varje **tidsstämpel** värdet har bara en post). Aviseringen utlöses så aldrig. I sådana fall måste du antingen:

- Lägga till en dummy variabel eller en befintlig variabel (t.ex. **$table**) att sortera korrekt med hjälp av den **sammanställd vid** fält.
- Konfigurera om regeln för att använda aviseringslogiken utifrån **Totalt antal överträdelser** i stället.

## <a name="log-alert-fired-unnecessarily"></a>Log avisering har utlösts onödigt

En konfigurerade [loggvarningsregel i Azure Monitor](../platform/alerts-log.md) kan utlösas oväntat när du visar den i [Azure Alerts](../platform/alerts-managing-alert-states.md). I följande avsnitt beskrivs några vanliga orsaker till.

### <a name="alert-triggered-by-partial-data"></a>Varningen aktiverades av partiella data

Log Analytics och Application Insights omfattas inmatning fördröjningar och bearbetning. När du har kört en loggvarningsfråga kan det hända att inga data är tillgängliga eller bara vissa data är tillgängliga. Mer information finns i [logga tid för inmatning av data i Azure Monitor](../platform/data-ingestion-time.md).

Beroende på hur du konfigurerat varningsregeln kan feltändning inträffa om det finns ingen data eller partiella data i loggar vid tidpunkten för avisering körning. I sådana fall rekommenderar vi att du kan ändra aviseringsfråga eller konfiguration. 

Till exempel om du konfigurerar loggvarningsregel ska utlösas när antalet resultat från en analytics-fråga är mindre än 5 utlöses aviseringen när det finns ingen data (ingen post) eller ofullständiga resultat (en post). Men efter fördröjning inmatning av data med fullständig samma fråga kan ge ett resultat av 10 poster.

### <a name="alert-query-output-is-misunderstood"></a>Aviseringsfråga utdata är tror många

Du kan ange logiken för loggaviseringar i en analytics-fråga. Analytics-fråga kan använda olika stordata och matematiska funktioner. Aviseringen tjänsten körs frågan med intervall som angetts med data för en angiven tidsperiod. Tjänsten avisering gör små ändringar i frågan baserat på typ av avisering. Du kan visa den här ändringen i den **fråga som ska köras** avsnittet på den **konfigurera signallogiken** skärmen:

![Fråga som ska köras](media/alert-log-troubleshoot/LogAlertPreview.png)

Den **fråga som ska köras** rutan är varning Loggtjänsten körs. Om du vill förstå vilka aviseringsfrågan utdata kan vara innan du skapar aviseringen kan du köra den angivna frågan och tidsintervallet via den [analysportalen](../log-query/portals.md) eller [API för textanalys](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Log-aviseringen har inaktiverats

Följande avsnitt listar några orsaker till varför Azure Monitor kan inaktivera den [loggvarningsregler](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Resursen där aviseringen skapades inte längre finns

Loggvarningsregler som skapas i Azure Monitor rikta en specifik resurs som en Azure Log Analytics-arbetsyta, en Azure Application Insights-app och en Azure-resurs. Aviseringen Loggtjänsten körs en analytics-fråga som angetts i regeln för det angivna målet. Men när regeln har skapats, användare går ofta du vidare till ta bort från Azure, eller flytta i Azure – målet för aviseringsregeln log. Körningen av regeln misslyckas eftersom målet för aviseringsregeln inte längre är giltigt.

I sådana fall kan Azure Monitor inaktiverar log aviseringen och ser till att du inte faktureras onödan när regeln inte kan köras kontinuerligt volymer under (t.ex. en vecka). Du hittar den exakta tidpunkt när Azure Monitor inaktiverad log aviseringen via [Azure-aktivitetsloggen](../../azure-resource-manager/resource-group-audit.md). I Azure-aktivitetsloggen läggs en händelse när Azure Monitor inaktiverar log varningsregeln.

Följande exempelhändelse i aktivitetsloggen för Azure är för en varningsregel som har inaktiverats på grund av ett kontinuerliga fel.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Fråga som används i en logg-avisering är inte giltig

Varje loggvarningsregel som skapats i Azure Monitor som en del av dess konfiguration måste ange en analytics-fråga som tjänsten avisering ska köras med jämna mellanrum. Analytics-frågan kanske har rätt syntax vid tidpunkten för skapande eller uppdatering. Men ibland under en viss tidsperiod frågan i loggvarningsregel kan utveckla syntaxproblem och orsaka regelutförandet misslyckas. Några vanliga orsaker till varför en analytics-fråga i en loggvarningsregel kan utveckla fel är:

- Frågan skrivs till [körs över flera resurser](../log-query/cross-workspace-query.md). Och en eller flera av de angivna resurserna finns inte längre.
- Det har inga dataflöde för analytics-plattformen. Den [Frågekörningen ger ett fel](https://dev.loganalytics.io/documentation/Using-the-API/Errors) eftersom det finns inga data för den angivna frågan.
- Ändringar i [frågespråket](https://docs.microsoft.com/azure/kusto/query/) inkludera ett reviderade format för kommandon och funktioner. Frågan som anges tidigare i en aviseringsregel är därför inte längre giltig.

[Azure Advisor](../../advisor/advisor-overview.md) varnar dig om det här beteendet. En rekommendation har lagts till för den specifika loggvarningsregel på Azure Advisor under kategorin för hög tillgänglighet med medelstor inverkan och en beskrivning av ”reparation din loggvarningsregel så övervakning”. Om en avisering fråga i loggvarningsregel inte ändras när Azure Advisor tillhandahåller en rekommendation i sju dagar, Azure Monitor inaktiveras log aviseringen och se till att du inte faktureras onödan när regeln inte kan köras hela tiden för en period (volymer) som en vecka).

Du kan hitta den exakta tidpunkt när Azure Monitor inaktiverad log regeln genom att söka efter en händelse i [Azure-aktivitetsloggen](../../azure-resource-manager/resource-group-audit.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [loggaviseringar i Azure](../platform/alerts-unified-log.md).
- Läs mer om [Application Insights](../../azure-monitor/app/analytics.md).
- Läs mer om [logga frågor](../log-query/log-query-overview.md).
