---
title: Fråga över resurser med Azure Monitor | Microsoft Docs
description: Den här artikeln beskriver hur du kan fråga mot resurser från flera arbets ytor och App Insights-appen i din prenumeration.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/05/2019
ms.openlocfilehash: 4740034bd970f42833125fa43bfdf72f710ac147
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249612"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Utföra kors resurs logg frågor i Azure Monitor  

Tidigare med Azure Monitor kunde du bara analysera data från den aktuella arbets ytan och den begränsade möjligheten att fråga över flera arbets ytor som definierats i din prenumeration.  Dessutom kan du bara söka efter telemetri som samlats in från ditt webbaserade program med Application Insights direkt i Application Insights eller från Visual Studio. Detta gjorde också det en utmaning att internt analysera drift-och program data tillsammans.

Nu kan du fråga inte bara över flera Log Analytics arbets ytor, utan även data från en speciell Application Insights app i samma resurs grupp, en annan resurs grupp eller en annan prenumeration. Detta ger dig en systemomfattande vy över dina data. Du kan bara utföra dessa typer av frågor i [Log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Begränsningar för kors resurs frågor 

* Antalet Application Insights-resurser och Log Analytics arbets ytor som du kan ta med i en enskild fråga är begränsade till 100.
* Frågan över resurser stöds inte i View Designer. Du kan redigera en fråga i Log Analytics och fästa den på Azure-instrumentpanelen för att [visualisera en logg fråga](../learn/tutorial-logs-dashboards.md). 
* Frågan över resurser i logg aviseringar stöds i det nya [scheduledQueryRules-API: et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Som standard använder Azure Monitor den [äldre Log Analytics varnings-API: n](../platform/api-alerts.md) för att skapa nya logg aviserings regler från Azure Portal, såvida du inte växlar från [äldre API för logg aviseringar](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Efter växeln blir det nya API: t standardvärdet för nya varnings regler i Azure Portal och du kan skapa frågor om aviserings regler för kors resurs. Du kan skapa frågor om kors resurs frågor om loggen utan att göra växeln med hjälp av [Azure Resource Manager mall för scheduledQueryRules-API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – men den här varnings regeln kan hanteras även om [scheduledQueryRules-API: n](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) inte är Azure Portal.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Fråga i Log Analytics arbets ytor och från Application Insights
Om du vill referera till en annan arbets yta i din fråga använder du ID för [*arbets ytan*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) och för en app från Application Insights använder du [*app*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression) -ID: t.  

### <a name="identifying-workspace-resources"></a>Identifiera arbets ytans resurser
Följande exempel visar frågor i Log Analytics arbets ytor för att returnera sammanfattade antal loggar från uppdaterings tabellen på en arbets yta med namnet *ContosoRetail*. 

Att identifiera en arbets yta kan utföras på flera sätt:

* Resurs namn – är ett läsbart namn på arbets ytan, ibland kallat *komponent namn*. 

    `workspace("contosoretail-it").Update | count`

* Kvalificerat namn – är det fullständiga namnet på arbets ytan, som består av prenumerations namnet, resurs gruppen och komponent namnet i följande format: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Eftersom Azures prenumerations namn inte är unika kan den här identifieraren vara tvetydig. 
    >

* Arbetsyte-ID – ett arbetsyte-ID är den unika, oföränderliga, identifierare som tilldelats varje arbets yta som representerar en globalt unik identifierare (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure-resurs-ID – den Azure-definierade unika identiteten för arbets ytan. Du använder resurs-ID: t när resurs namnet är tvetydigt.  För arbets ytor är formatet: */Subscriptions/subscriptionId/ResourceGroups/resourceGroup/providers/Microsoft. OperationalInsights/arbets ytor/componentName*.  

    Exempel:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identifiera ett program
I följande exempel returneras ett sammanfattat antal begär Anden som gjorts mot en app med namnet *fabrikamapp* i Application Insights. 

Att identifiera ett program i Application Insights kan utföras med ett *app-uttryck (Identifier)* .  Argumentet *Identifier* anger appen med något av följande:

* Resurs namn – är ett läsligt namn på appen, som ibland kallas för *komponent namn*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Att identifiera ett program efter namn förutsätter att det är unikt för alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet, Miss lyckas frågan på grund av tvetydigheten. I så fall måste du använda någon av de andra identifierarna.

* Kvalificerat namn – är det fullständiga namnet på appen, som består av prenumerations namnet, resurs gruppen och komponent namnet i följande format: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Eftersom Azures prenumerations namn inte är unika kan den här identifieraren vara tvetydig. 
    >

* ID – programmets GUID för programmet.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure-resurs-ID – den Azure-definierade unika identiteten för appen. Du använder resurs-ID: t när resurs namnet är tvetydigt. Formatet är: */Subscriptions/subscriptionId/ResourceGroups/resourceGroup/providers/Microsoft. OperationalInsights/Components/componentName*.  

    Exempel:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Utföra en fråga över flera resurser
Du kan fråga flera resurser från någon av dina resurs instanser, och dessa kan vara arbets ytor och appar kombinerade.
    
Exempel för fråga över två arbets ytor:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Använda frågor över flera resurser för flera resurser
När du använder frågor över flera resurser för att korrelera data från flera arbets ytor Log Analytics och Application Insights resurser, kan frågan bli komplex och svår att underhålla. Du bör använda [funktioner i Azure Monitor logg frågor](functions.md) för att avgränsa fråge logiken från omfånget för frågans resurser, vilket fören klar fråge strukturen. I följande exempel visas hur du kan övervaka flera Application Insights resurser och visualisera antalet misslyckade förfrågningar efter program namn. 

Skapa en fråga som refererar till omfånget för Application Insights resurser. Kommandot `withsource= SourceApp` lägger till en kolumn som anger det program namn som skickade loggen. [Spara frågan som funktion](functions.md#create-a-function) med aliaset _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Du kan nu [använda den här funktionen](../../azure-monitor/log-query/functions.md#use-a-function) i en kors resurs fråga som liknar följande. Funktions Ali Aset _applicationsScoping_ returnerar unionen av begär ande tabellen från alla definierade program. Frågan filtrerar sedan efter misslyckade förfrågningar och visualiserar trender efter program. Operatorn _parse_ är valfri i det här exemplet. Den extraherar program namnet från egenskapen _SourceApp_ .

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Den här metoden kan inte användas med logg aviseringar eftersom åtkomst verifieringen av aviserings regel resurserna, inklusive arbets ytor och program, utförs när aviseringen skapas. Det går inte att lägga till nya resurser till funktionen när aviseringen har skapats. Om du föredrar att använda funktionen för resurs omfång i logg aviseringar måste du redigera aviserings regeln i portalen eller med en Resource Manager-mall för att uppdatera de omfångs resurserna. Du kan också inkludera listan över resurser i logg aviserings frågan.


![Timechart](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Nästa steg

- Granska [analysera loggdata i Azure Monitor](log-query-overview.md) om du vill ha en översikt över logg frågor och hur Azure Monitor loggdata är strukturerade.
- Granska [Azure Monitor logg frågor](query-language.md) för att visa alla resurser för Azure Monitor logg frågor.
