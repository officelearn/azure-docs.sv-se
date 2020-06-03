---
title: Felsöka logg aviseringar i Azure Monitor | Microsoft Docs
description: Vanliga problem, fel och lösningar för logg aviserings regler i Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: ab6ee597cfdc5d169bd33b77a061880b19e134b6
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300311"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Felsöka logg aviseringar i Azure Monitor  

Den här artikeln visar hur du löser vanliga problem med logg aviseringar i Azure Monitor. Den innehåller också lösningar på vanliga problem med funktioner och konfiguration av logg aviseringar.

Term *logg aviseringar* beskriver regler som utlöses baserat på en logg fråga i en [Azure Log Analytics-arbetsyta](../learn/tutorial-viewdata.md) eller i [Azure Application insikter](../../azure-monitor/app/analytics.md). Lär dig mer om funktioner, terminologi och typer i [logg aviseringar i Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Den här artikeln tar inte hänsyn till fall där Azure Portal visar en varnings regel som utlöses och en avisering inte utförs av en associerad åtgärds grupp. I sådana fall kan du läsa informationen i [skapa och hantera åtgärds grupper i Azure Portal](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Logg aviseringen startades inte

Här följer några vanliga orsaker till varför tillstånd för en konfigurerad [logg aviserings regel i Azure Monitor](../platform/alerts-log.md) inte visas [som *utlöst* när den förväntas](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Data hämtnings tid för loggar

En logg avisering kör regelbundet din fråga baserat på [Log Analytics](../learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/analytics.md). Eftersom Azure Monitor bearbetar många terabyte data från tusentals kunder från varierande källor över hela världen, är tjänsten sårbar för att variera tids fördröjningar. Mer information finns i data Inhämtnings [tid i Azure Monitor loggar](../platform/data-ingestion-time.md).

För att undvika fördröjningar väntar systemet och gör om aviserings frågan flera gånger om den hittar nödvändiga data som inte har matats in ännu. Systemet har en exponentiellt ökande vänte tid. Logg aviseringen utlöses endast efter att data är tillgängliga, så fördröjningen kan bero på långsam inmatning av loggdata.

### <a name="incorrect-time-period-configured"></a>En felaktig tids period har kon figurer ATS

Som det beskrivs i artikeln om [terminologi för logg aviseringar](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)anger tids perioden som anges i konfigurationen tidsintervallet för frågan. Frågan returnerar endast poster som har skapats inom det här intervallet.

Tids perioden begränsar de data som hämtas för en logg fråga för att förhindra missbruk, och den kringgår alla tids kommandon (t. ex. **sedan**) som används i en logg fråga. Om tids perioden till exempel är inställd på 60 minuter och frågan körs med 1:15 PM, används endast poster som skapats mellan 12:15 PM och 1:15 PM för logg frågan. Om logg frågan använder ett Time-kommando som **sedan används (1d)** använder frågan fortfarande data mellan 12:15 och 1:15 PM eftersom tids perioden har angetts till intervallet.

Kontrol lera att tids perioden i konfigurationen matchar din fråga. I exemplet ovan, om logg frågan använder **sedan (1d)** med den gröna markören, ska tids perioden vara inställd på 24 timmar eller 1 440 minuter (anges i rött). Den här inställningen säkerställer att frågan körs som avsett.

![Tidsperiod](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Alternativet för att förhindra varningar har angetts

Som beskrivs i steg 8 i artikeln om hur [du skapar en logg aviserings regel i Azure Portal](../platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal), visar logg aviseringar ett alternativ för att **förhindra** utlösare och meddelande åtgärder för en konfigurerad tids period. Därför kan du tänka på att en avisering inte har startats. Det var faktiskt Fire men undertryckdes.  

![Ignorera aviseringar](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Varnings regeln för mått mätning är felaktig

*Varnings loggs aviseringar* är en undertyp till logg aviseringar som har särskilda funktioner och en begränsad syntax för aviserings frågor. En regel för en mätnings logg avisering kräver att frågeresultatet är en tids serie för mått. Det vill säga att utdata är en tabell med distinkta, lika stora tids perioder tillsammans med motsvarande aggregerade värden.

Du kan välja att ha ytterligare variabler i tabellen tillsammans med **AggregatedValue**. Dessa variabler kan användas för att sortera tabellen.

Anta till exempel att en regel för varnings loggen för mått har kon figurer ATS som:

- Fråga för`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Tids period på 6 timmar
- Tröskelvärde på 50
- Aviserings logik av tre överträdelser i följd
- **Sammanställt på** valt som **$Table**

Eftersom kommandot innehåller **Sammanfattning... av** och ger två variabler (**tidsstämpel** och **$table**) väljer systemet **$Table** för **agg regering**. I systemet sorteras resultat tabellen efter fältet **$Table** , som du ser i följande skärm bild. Sedan tittar den på flera **AggregatedValue** -instanser för varje tabell typ (t. ex. **availabilityResults**) för att se om det fanns tre eller fler överträdelser i följd.

![Mätning av mått fråga med flera värden](media/alert-log-troubleshoot/LogMMQuery.png)

Eftersom **agg regering** på har definierats på **$Table**, sorteras data i en **$Table** kolumn (anges i rött). Sedan kan vi gruppera och leta efter typer av fältet **aggregera efter** .

Till exempel, för **$Table**, betraktas värden för **availabilityResults** som ett rityta/en entitet (anges i orange). I det här värde området/entiteten söker aviserings tjänsten efter tre överträdelser i följd (anges i grönt). Överträdelserna utlöser en avisering för tabell värdets **availabilityResults**.

Om tre överträdelser i följd inträffar för andra värden för **$Table**, utlöses ett annat aviserings meddelande för samma sak. Aviserings tjänsten sorterar automatiskt värdena i ett rityta/en entitet (anges i orange) efter tid.

Anta nu att regeln för varnings loggen för mått har ändrats och att frågan var `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` . Resten av konfigurationen befann sig på samma sätt som tidigare, inklusive aviserings logiken för tre överträdelser i följd. Alternativet **aggregera efter** i det här fallet är **tidsstämpel** som standard. Det finns bara ett värde i frågan för **Sammanfattning... efter** (dvs. **tidsstämpel**). Precis som i det tidigare exemplet skulle utdata i slutet av körningen se ut så här.

   ![Mätning av mått fråga med ett eget värde](media/alert-log-troubleshoot/LogMMtimestamp.png)

Eftersom **aggregerad vid** har definierats för **timestamp**, sorteras data i kolumnen **tidsstämpel** (anges i rött). Sedan grupperas efter **tidsstämpel**. Till exempel kommer värden för att `2018-10-17T06:00:00Z` betraktas som ett område/en entitet (anges i orange). I det här värde området/entiteten hittar aviserings tjänsten inga efterföljande överträdelser (eftersom varje **tidsstämpel** -värde bara har en post). Så att aviseringen aldrig utlöses. I sådana fall måste användaren antingen:

- Lägg till en dummy-variabel eller en befintlig variabel (t. ex. **$Table**) för att sortera på rätt sätt med hjälp av fältet **agg regerings vid** .
- Konfigurera om aviserings regeln så att den använder aviserings logik baserat på **Total överträdelse** i stället.

## <a name="log-alert-fired-unnecessarily"></a>Logg aviseringen utlöstes i onödan

En konfigurerad [logg aviserings regel i Azure Monitor](../platform/alerts-log.md) kan utlösas oväntad när du visar den i [Azure-aviseringar](../platform/alerts-managing-alert-states.md). I följande avsnitt beskrivs några vanliga orsaker.

### <a name="alert-triggered-by-partial-data"></a>Avisering utlöst av partiella data

Log Analytics och Application Insights utsätts för användnings fördröjning och bearbetning. När du kör en logg aviserings fråga kanske du upptäcker att inga data är tillgängliga eller att endast vissa data är tillgängliga. Mer information finns i logg data Inhämtnings [tid i Azure Monitor](../platform/data-ingestion-time.md).

Beroende på hur du har konfigurerat varnings regeln kan fel utlösare inträffa om det inte finns några data eller ofullständiga data i loggar vid tidpunkten för aviserings körningen. I sådana fall rekommenderar vi att du ändrar aviserings frågan eller konfigurationen.

Om du till exempel konfigurerar logg aviserings regeln så att den utlöses när antalet resultat från en Analytics-fråga är mindre än 5 utlöses aviseringen när det inte finns några data (noll Record) eller partiella resultat (en post). Men efter fördröjningen av data inmatning kan samma fråga med fullständiga data ge resultatet 10 poster.

### <a name="alert-query-output-is-misunderstood"></a>Aviserings frågans utdata kan inte tolkas

Du anger logiken för logg aviseringar i en Analytics-fråga. Analys frågan kan använda olika Big data och matematiska funktioner. Aviserings tjänsten kör frågan med intervall som anges med data under en angiven tids period. Aviserings tjänsten utför diskreta ändringar i frågan baserat på aviserings typen. Du kan visa den här ändringen i avsnittet **fråga som ska utföras** på skärmen **Konfigurera signal logik** :

![Fråga som ska köras](media/alert-log-troubleshoot/LogAlertPreview.png)

Rutan **fråga som ska utföras** är det som logg aviserings tjänsten körs på. Om du vill förstå hur aviserings frågans utdata kan vara innan du skapar aviseringen kan du köra den angivna frågan och TimeSpan via [Analytics-portalen](../log-query/portals.md) eller analys- [API: et](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Logg avisering har inaktiverats

I följande avsnitt visas några orsaker till varför Azure Monitor kan inaktivera [logg varnings regeln](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Resurs där aviseringen skapades finns inte längre

Logga varnings regler som skapats i Azure Monitor rikta en speciell resurs som en Azure Log Analytics-arbetsyta, en Azure Application insikts-app och en Azure-resurs. Logg aviserings tjänsten kör sedan en analys fråga som anges i regeln för det angivna målet. Men när regeln har skapats, går användarna ofta vidare att ta bort från Azure, eller flytta i Azure – målet för logg aviserings regeln. Eftersom målet för varnings regeln inte längre är giltigt, Miss lyckas körningen av regeln.

I sådana fall inaktiverar Azure Monitor logg aviseringen och ser till att du inte faktureras i onödan när regeln inte kan köras kontinuerligt för en justerbar period (t. ex. en vecka). Du kan ta reda på exakt hur lång tid Azure Monitor har inaktiverat logg aviseringen via [Azure aktivitets loggen](../../azure-resource-manager/management/view-activity-logs.md). I Azure aktivitets logg läggs en händelse till när Azure Monitor inaktiverar logg varnings regeln.

Följande exempel händelse i Azure aktivitets loggen är för en varnings regel som har inaktiverats på grund av ett kontinuerligt haveri.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Frågan som används i en logg avisering är inte giltig

Varje logg aviserings regel som skapas i Azure Monitor som en del av konfigurationen måste ange en analys fråga som aviserings tjänsten ska köras regelbundet. Analys frågan kan ha rätt syntax vid tidpunkten för att skapa eller uppdatera regeln. Men ibland under en viss tids period kan frågan som anges i logg aviserings regeln utveckla syntaxfel och orsaka att regel körningen inte fungerar. Några vanliga orsaker till varför en Analytics-fråga som finns i en logg aviserings regel kan utveckla fel:

- Frågan skrivs för att [köras över flera resurser](../log-query/cross-workspace-query.md). Och en eller flera av de angivna resurserna finns inte längre.
- [Måttet för mått mått typ logg aviseringen](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) som kon figurer ATS har en varnings fråga som inte överensstämmer med syntaxens norm
- Det finns inget data flöde för analys plattformen. [Frågekörningen ger ett fel](https://dev.loganalytics.io/documentation/Using-the-API/Errors) eftersom det inte finns några data för den angivna frågan.
- Ändringar i [frågespråket](https://docs.microsoft.com/azure/kusto/query/) innehåller ett ändrat format för kommandon och funktioner. Den fråga som tillhandahölls tidigare i en varnings regel är inte längre giltig.

[Azure Advisor](../../advisor/advisor-overview.md) varnar dig om det här beteendet. En rekommendation läggs till för den speciella logg aviserings regeln på Azure Advisor, under kategorin med hög tillgänglighet med medelhög påverkan och en beskrivning av "reparera din logg aviserings regel för att säkerställa övervakning". Om en varnings fråga i logg varnings regeln inte är rättad efter att Azure Advisor har angett en rekommendation i sju dagar, inaktiverar Azure Monitor logg aviseringen och ser till att du inte faktureras i onödan om regeln inte kan köras kontinuerligt för en justerbar period (t. ex. en vecka).

Du hittar den exakta tiden när Azure Monitor inaktiverade logg aviserings regeln genom att leta efter en händelse i [Azure aktivitets loggen](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [logg aviseringar i Azure](../platform/alerts-unified-log.md).
- Läs mer om [Application Insights](../../azure-monitor/app/analytics.md).
- Läs mer om [logg frågor](../log-query/log-query-overview.md).
