---
title: Felsöka loggaviseringar i Azure Monitor | Microsoft-dokument
description: Vanliga problem, fel och lösningar för loggaviseringsregler i Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249040"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Felsöka loggaviseringar i Azure Monitor  

Den här artikeln visar hur du löser vanliga problem som kan uppstå när du konfigurerar loggaviseringar i Azure Monitor. Det ger också lösningar på vanliga problem med funktioner eller konfiguration av loggvarningar.

Termen *loggaviseringar* beskriver regler som avfyras baserat på en loggfråga i en [Azure Log Analytics-arbetsyta](../learn/tutorial-viewdata.md) eller i [Azure Application Insights](../../azure-monitor/app/analytics.md). Läs mer om funktioner, terminologi och typer i [Loggaviseringar i Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Den här artikeln tar inte hänsyn till fall där Azure-portalen visar en varningsregel som utlöses och ett meddelande inte utförs av en associerad åtgärdsgrupp. I sådana fall finns information i [Skapa och hantera åtgärdsgrupper i Azure-portalen](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Loggvarning brandade inte

Här är några vanliga orsaker till att tillståndet för en konfigurerad [loggaviseringsregel i Azure Monitor](../platform/alerts-log.md) inte visas [som *avfyrat* när det är förväntat](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Tid för datainmatning för loggar

En loggavisering kör regelbundet din fråga baserat på [Log Analytics](../learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/analytics.md). Eftersom Azure Monitor bearbetar många terabyte data från tusentals kunder från olika källor över hela världen, är tjänsten känslig för olika tidsfördröjningar. Mer information finns [i Datainmatningstid i Azure Monitor-loggar](../platform/data-ingestion-time.md).

För att minska fördröjningar väntar och försöker systemet igen aviseringsfrågan flera gånger om den hittar de nödvändiga data som behövs ännu inte har förtärts. Systemet har en exponentiellt ökande väntetid inställd. Loggaviseringen utlöses först när data är tillgängliga, så fördröjningen kan bero på långsam inmatning av loggdata.

### <a name="incorrect-time-period-configured"></a>Felaktig tidsperiod konfigurerad

Som beskrivs i artikeln om [terminologi för loggaviseringar](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)anger den tidsperiod som anges i konfigurationen tidsintervallet för frågan. Frågan returnerar endast poster som har skapats inom det här intervallet.

Tidsperioden begränsar de data som hämtas för en loggfråga för att förhindra missbruk, och det kringgår alla tidskommandon (som **sedan)** som används i en loggfråga. Om till exempel tidsperioden är inställd på 60 minuter och frågan körs klockan 13:15 används endast poster som skapats mellan 12:15 och 13:15 för loggfrågan. Om loggfrågan använder ett tidskommando som **sedan (1d)** använder frågan fortfarande bara data mellan 12:15 och 13:15 eftersom tidsperioden är inställd på det intervallet.

Kontrollera att tidsperioden i konfigurationen matchar din fråga. Om loggfrågan används **sedan (1d)** med den gröna markören ska tidsperioden anges till 24 timmar eller 1 440 minuter (anges i rött). Den här inställningen säkerställer att frågan körs som avsett.

![Tidsperiod](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Alternativet Utelämna aviseringar är inställt

Som beskrivs i steg 8 i artikeln om [att skapa en loggaviseringsregel i Azure-portalen](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)innehåller loggaviseringar ett alternativ för **undertryckning** av aviseringar för att undertrycka utlösande åtgärder och meddelandeåtgärder under en konfigurerad tid. Som ett resultat, kanske du tror att en varning inte brand. I själva verket sköt det men undertrycktes.  

![Utelämna aviseringar](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Måttvarningsregeln är felaktig

*Måttmätningsloggaviseringar* är en undertyp av loggaviseringar som har särskilda funktioner och en begränsad aviseringsfrågasyntax. En regel för en måttmätningsloggavisering kräver att frågeutdata är en måtttidsserie. Det innebär att utdata är en tabell med distinkta tidsperioder av lika storlek tillsammans med motsvarande aggregerade värden.

Du kan välja att ha ytterligare variabler i tabellen tillsammans med **AggregatedValue**. Dessa variabler kan användas för att sortera tabellen.

Anta till exempel att en regel för en måttmätningsloggavisering har konfigurerats som:

- Fråga av`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Tidsperiod på 6 timmar
- Tröskelvärde på 50
- Varningslogik för tre på varandra följande överträdelser
- **Sammanlagt vid** vald **som $table**

Eftersom kommandot innehåller **sammanfatta ... av** och ger två variabler **(tidsstämpel** och **$table)** väljer systemet **$table** för **Aggregate Upon**. Systemet sorterar resultattabellen efter **fältet $table,** som visas i följande skärmbild. Sedan tittar den på flera **AggregatedValue-instanser** för varje tabelltyp (som **tillgänglighetResultat)** för att se om det fanns tre eller fler på varandra följande överträdelser.

![Körning av måttmätningsfråga med flera värden](media/alert-log-troubleshoot/LogMMQuery.png)

Eftersom **Aggregate Upon** definieras på **$table**sorteras data på en **$table** kolumn (markerad med rött). Sedan grupperar och letar vi efter typer av fältet **Aggregera på.**

För **$table**betraktas till exempel värden för **tillgänglighetResultat** som en ritplan/entitet (anges i orange). I det här värdeområdet/entiteten söker varningstjänsten efter tre på varandra följande överträdelser (anges i grönt). Överträdelserna utlöser en avisering för **tabellvärdetillgångResultat**.

På samma sätt utlöses ett annat varningsmeddelande för samma sak om tre på varandra följande överträdelser inträffar för något annat värde **i $table.** Aviseringstjänsten sorterar automatiskt värdena i ett område/entitet (indikerat i orange) efter tid.

Anta nu att regeln för måttmätningsloggvarningen `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`har ändrats och frågan var . Resten av konfigurationen förblev densamma som tidigare, inklusive varningslogiken för tre på varandra följande överträdelser. Alternativet **Aggregera på** i det här fallet är **tidsstämpel** som standard. Endast ett värde anges i frågan för **sammanfatta ... av** (det **timestamp**vill Liksom det tidigare exemplet skulle produktionen i slutet av körningen illustreras som följer.

   ![Körning av måttmätningsfråga med singularvärde](media/alert-log-troubleshoot/LogMMtimestamp.png)

Eftersom **Aggregate Upon** definieras i **tidsstämpel**sorteras data i **tidsstämpelkolumnen** (markerad med rött). Sedan grupperar vi efter **tidsstämpel.** Värden för `2018-10-17T06:00:00Z` betraktas till exempel som ett område/entitet (anges i orange). I det här värdeområdet/entiteten hittar aviseringstjänsten inga på varandra följande överträdelser (eftersom varje **tidsstämpelvärde** bara har en post). Så varningen utlöses aldrig. I sådana fall skall användaren antingen

- Lägg till en dummyvariabel **$table**eller en befintlig variabel (t.o.$table) om du vill sortera korrekt med hjälp av fältet **Aggregera på.**
- Konfigurera om varningsregeln om du vill använda varningslogik baserat på **total överträdelse** i stället.

## <a name="log-alert-fired-unnecessarily"></a>Loggvarning avfyrad i onödan

En konfigurerad [loggaviseringsregel i Azure Monitor](../platform/alerts-log.md) kan utlösas oväntat när du visar den i [Azure Alerts](../platform/alerts-managing-alert-states.md). I följande avsnitt beskrivs några vanliga orsaker.

### <a name="alert-triggered-by-partial-data"></a>Avisering som utlöses av partiella data

Logganalys och programinsikter är föremål för inmatningsfördröjningar och bearbetning. När du kör en loggaviseringsfråga kan det hända att inga data är tillgängliga eller att endast vissa data är tillgängliga. Mer information finns [i Logga inmatningstid för data i Azure Monitor](../platform/data-ingestion-time.md).

Beroende på hur du konfigurerade varningsregeln kan feltändning inträffa om det inte finns några data eller partiella data i loggar vid tidpunkten för aviseringskörningen. I sådana fall rekommenderar vi att du ändrar aviseringsfrågan eller konfigurationen.

Om du till exempel konfigurerar loggvarningsregeln som ska utlösas när antalet resultat från en analysfråga är mindre än 5, utlöses aviseringen när det inte finns några data (nollpost) eller partiella resultat (en post). Men efter fördröjningen för datainmatning kan samma fråga med fullständiga data ge ett resultat av 10 poster.

### <a name="alert-query-output-is-misunderstood"></a>Utdata för aviseringsfråga missförstås

Du anger logiken för loggaviseringar i en analysfråga. Analysfrågan kan använda olika stordata och matematiska funktioner. Aviseringstjänsten kör frågan med intervall som anges med data för en angiven tidsperiod. Aviseringstjänsten gör subtila ändringar i frågan baserat på aviseringstypen. Du kan visa den här ändringen i avsnittet **Fråga som ska köras** på skärmen **Konfigurera signallogik:**

![Fråga som ska köras](media/alert-log-troubleshoot/LogAlertPreview.png)

Rutan **Fråga som ska köras** är vad loggaviseringstjänsten körs. Om du vill förstå vad aviseringsfråganutdata kan vara innan du skapar aviseringen kan du köra den angivna frågan och tidsintervallet via [Analytics-portalen](../log-query/portals.md) eller [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Loggvarning har inaktiverats

I följande avsnitt visas några orsaker till varför Azure Monitor kan inaktivera [loggvarningsregeln](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Resurs där aviseringen skapades finns inte längre

Loggaviseringsregler som skapats i Azure Monitor riktar in sig på en viss resurs som en Azure Log Analytics-arbetsyta, en Azure Application Insights-app och en Azure-resurs. Loggvarningstjänsten kör sedan en analysfråga som anges i regeln för det angivna målet. Men när regeln har skapats fortsätter användarna ofta att ta bort från Azure – eller flytta in i Azure – målet för loggaviseringsregeln. Eftersom målet för aviseringsregeln inte längre är giltigt misslyckas körningen av regeln.

I sådana fall inaktiverar Azure Monitor loggaviseringen och ser till att du inte faktureras i onödan när regeln inte kan köras kontinuerligt under ansenlig period (som en vecka). Du kan ta reda på den exakta tiden när Azure Monitor inaktiverade loggaviseringen via [Azure Activity Log](../../azure-resource-manager/management/view-activity-logs.md). I Azure Activity Log läggs en händelse till när Azure Monitor inaktiverar loggaviseringsregeln.

Följande exempelhändelse i Azure Activity Log är för en aviseringsregel som har inaktiverats på grund av ett kontinuerligt fel.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Frågan som används i en loggavisering är ogiltig

Varje loggaviseringsregel som skapas i Azure Monitor som en del av konfigurationen måste ange en analysfråga som aviseringstjänsten ska köras med jämna mellanrum. Analysfrågan kan ha rätt syntax när regeln skapas eller uppdateras. Men ibland, under en tidsperiod, kan frågan som finns i loggaviseringsregeln utveckla syntaxproblem och orsaka att regelkörningen misslyckas. Några vanliga orsaker till att en analysfråga som tillhandahålls i en loggvarningsregel kan utveckla fel är:

- Frågan skrivs för att [köras över flera resurser](../log-query/cross-workspace-query.md). Och en eller flera av de angivna resurserna finns inte längre.
- [Logvarning för måttmätning](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) som konfigurerats har en aviseringsfråga inte överensstämmer med syntaxnormerna
- Det har inte funnits något dataflöde till analysplattformen. [Frågekörningen ger ett fel](https://dev.loganalytics.io/documentation/Using-the-API/Errors) eftersom det inte finns några data för den angivna frågan.
- Ändringar i [frågespråket](https://docs.microsoft.com/azure/kusto/query/) innehåller ett reviderat format för kommandon och funktioner. Så frågan som angavs tidigare i en varningsregel är inte längre giltig.

[Azure Advisor](../../advisor/advisor-overview.md) varnar dig om det här beteendet. En rekommendation läggs till för den specifika loggaviseringsregeln på Azure Advisor, under kategorin Hög tillgänglighet med medelhög påverkan och en beskrivning av "Reparera loggvarningsregeln för att säkerställa övervakning." Om en aviseringsfråga i loggvarningsregeln inte rättas till efter att Azure Advisor har gett en rekommendation i sju dagar inaktiverar Azure Monitor loggaviseringen och ser till att du inte faktureras i onödan när regeln inte kan köras kontinuerligt under en ansenlig period ( som en vecka).

Du kan hitta den exakta tiden när Azure Monitor inaktiverade loggaviseringsregeln genom att leta efter en händelse i [Azure Activity Log](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [loggaviseringar i Azure](../platform/alerts-unified-log.md).
- Läs mer om [Application Insights](../../azure-monitor/app/analytics.md).
- Läs mer om [loggfrågor](../log-query/log-query-overview.md).
