---
title: Fråga över resurser med Azure Monitor | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan fråga mot resurser från flera arbetsytor och appstatistik-appen i din prenumeration.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/05/2019
ms.openlocfilehash: 4740034bd970f42833125fa43bfdf72f710ac147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249612"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Utföra loggfrågor mellan resurser i Azure Monitor  

Tidigare med Azure Monitor kunde du bara analysera data från den aktuella arbetsytan och det begränsade din möjlighet att fråga över flera arbetsytor som definierats i din prenumeration.  Dessutom kan du bara söka telemetriobjekt som samlats in från ditt webbaserade program med Application Insights direkt i Application Insights eller Från Visual Studio. Detta gjorde det också till en utmaning att internt analysera operativa data och applikationsdata tillsammans.

Nu kan du fråga inte bara över flera Log Analytics-arbetsytor, utan även data från en specifik Application Insights-app i samma resursgrupp, en annan resursgrupp eller en annan prenumeration. Detta ger dig en systemomfattande vy över dina data. Du kan bara utföra dessa typer av frågor i [Log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Frågegränser för korsresursfrågor 

* Antalet Application Insights-resurser och Log Analytics-arbetsytor som du kan inkludera i en enda fråga är begränsat till 100.
* Frågan över flera resurser stöds inte i View Designer. Du kan skapa en fråga i Log Analytics och fästa den på Azure-instrumentpanelen för att [visualisera en loggfråga](../learn/tutorial-logs-dashboards.md). 
* Fråga över flera resurser i loggaviseringar stöds i det nya [schemalagdaQueryRules API:et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Som standard använder Azure Monitor det [äldre Log Analytics Alert API](../platform/api-alerts.md) för att skapa nya loggaviseringsregler från Azure-portalen, såvida du inte växlar från äldre [loggvarningar API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Efter växeln blir det nya API:et standard för nya varningsregler i Azure-portalen och du kan skapa regler för frågeloggar mellan resurser. Du kan skapa frågeloggvarningsregler för flera resurser utan att växla genom att använda [Azure Resource Manager-mallen för scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – men den här aviseringsregeln är hanterbar men [schemalagdQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och inte från Azure-portalen.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Fråga över Log Analytics-arbetsytor och från Application Insights
Om du vill referera till en annan arbetsyta i frågan använder du [*arbetsytans*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) identifierare och använder [*appidentifieraren*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression) för en app från Application Insights.  

### <a name="identifying-workspace-resources"></a>Identifiera arbetsyteresurser
I följande exempel visas frågor över Log Analytics-arbetsytor för att returnera summerade antal loggar från tabellen Uppdatera på en arbetsyta med namnet *contosoretail-it*. 

Identifiera en arbetsyta kan utföras på flera sätt:

* Resursnamn - är ett läsbart namn på arbetsytan, ibland kallat *komponentnamn*. 

    `workspace("contosoretail-it").Update | count`

* Kvalificerat namn - är arbetsytans "fullständiga namn" som består av prenumerationsnamnet, resursgruppen och komponentnamnet i det här formatet: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unika kan den här identifieraren vara tvetydig. 
    >

* Arbetsyte-ID - Ett arbetsområdes-ID är den unika, oföränderliga identifierare som tilldelats varje arbetsyta som representeras som en globalt unik identifierare (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Resource ID – den Azure-definierade unika identiteten på arbetsytan. Du kan använda resurs-ID när resursnamnet är tvetydigt.  För arbetsytor är formatet: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/workspaces/componentName*.  

    Ett exempel:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identifiera ett program
I följande exempel returneras ett sammanfattat antal begäranden som gjorts mot en app med namnet *fabrikamapp* i Application Insights. 

Identifiera ett program i Application Insights kan utföras med *app (Identifierare)* uttryck.  Argumentet *Identifierare* anger appen med något av följande:

* Resursnamn - är ett läsbart namn på appen, ibland kallat *komponentnamnet*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Identifiera ett program med namn förutsätter unikhet över alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet misslyckas frågan på grund av tvetydigheten. I det här fallet måste du använda någon av de andra identifierarna.

* Kvalificerat namn - är appens "fullständiga namn" som består av prenumerationsnamnet, resursgruppen och komponentnamnet i det här formatet: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unika kan den här identifieraren vara tvetydig. 
    >

* ID - appen GUID för programmet.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure Resource ID - appens Azure-definierade unika identitet. Du kan använda resurs-ID när resursnamnet är tvetydigt. Formatet är: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/komponenter/componentName*.  

    Ett exempel:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Utföra en fråga över flera resurser
Du kan fråga flera resurser från någon av dina resursinstanser, dessa kan vara arbetsytor och appar kombinerade.
    
Exempel på fråga på två arbetsytor:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Använda resursövergripande fråga för flera resurser
När du använder resursövergripande frågor för att korrelera data från flera Log Analytics-arbetsytor och Application Insights-resurser kan frågan bli komplex och svår att underhålla. Du bör utnyttja [funktioner i Azure Monitor-loggfrågor](functions.md) för att skilja frågelogiken från omfånget för frågeresurserna, vilket förenklar frågestrukturen. I följande exempel visas hur du kan övervaka flera Application Insights-resurser och visualisera antalet misslyckade begäranden med programnamn. 

Skapa en fråga som följande som refererar till omfattningen av Application Insights-resurser. Kommandot `withsource= SourceApp` lägger till en kolumn som anger programnamnet som skickade loggen. [Spara frågan som funktion](functions.md#create-a-function) med _aliasprogrammenScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Du kan nu [använda den här funktionen](../../azure-monitor/log-query/functions.md#use-a-function) i en resursfråga som följande. Funktionen alias _programScoping_ returnerar union av begäranden tabellen från alla definierade program. Frågan filtrerar sedan efter misslyckade begäranden och visualiserar trenderna efter program. _Parsa-operatorn_ är valfri i det här exemplet. Programnamnet extraheras från egenskapen _SourceApp._

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Den här metoden kan inte användas med loggaviseringar eftersom åtkomstvalidering av aviseringsregelresurserna, inklusive arbetsytor och program, utförs vid tidpunkten för att skapa aviseringar. Det går inte att lägga till nya resurser i funktionen när aviseringen har skapats. Om du föredrar att använda funktionen för resursomfång i loggaviseringar måste du redigera aviseringsregeln i portalen eller med en Resource Manager-mall för att uppdatera de begränsade resurserna. Du kan också inkludera listan över resurser i loggaviseringsfrågan.


![Tidsschema](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Nästa steg

- Granska [Analysera loggdata i Azure Monitor](log-query-overview.md) för en översikt över loggfrågor och hur Azure Monitor-loggdata är strukturerade.
- Granska [Azure Monitor-loggfrågor](query-language.md) för att visa alla resurser för Azure Monitor-loggfrågor.
