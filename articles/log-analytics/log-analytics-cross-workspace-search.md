---
title: Sök i resurser med Azure Log Analytics | Microsoft Docs
description: Den här artikeln beskrivs hur du kan fråga mot resurser från flera arbetsytor och App Insights i din prenumeration.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c6aa5cdd565a32367cf391625c1d5d6aca7ee186
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721207"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Utföra mellan resurser loggsökningar i Log Analytics  

Tidigare med Azure Log Analytics kunde du endast analysera data inifrån den aktuella arbetsytan och det begränsade möjligheten att fråga över flera arbetsytor som definierats i din prenumeration.  Dessutom kan du bara söka efter objekt för telemetri som samlas in från dina webbaserade program med Application Insights direkt i Application Insights eller från Visual Studio.  Detta också gjort det en utmaning att internt analysera operativa och programdata tillsammans.   

Nu kan du fråga inte bara över flera Log Analytics-arbetsytor, utan också data från en viss Application Insights-app i samma resursgrupp, en annan resursgrupp eller en annan prenumeration. Det ger en systemomfattande överblick över dina data.  Du kan bara utföra dessa typer av frågor i [Log Analytics](log-analytics-log-search-portals.md#log-analytics-page). Antalet resurser (Log Analytics-arbetsytor och Application Insights-app) som ska inkluderas i en enskild fråga är begränsad till 100. 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Fråga över Log Analytics-arbetsytor och från Application Insights
Om du vill referera till en annan arbetsyta i frågan, använda den [ *arbetsytan* ](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) identifierare, och för en app från Application Insights, använder du den [ *app* ](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)identifierare.  

### <a name="identifying-workspace-resources"></a>Identifiera resurser för arbetsyta
Följande exempel visar frågor i Log Analytics-arbetsytor att returnera sammanfattande antal loggar från tabellen uppdatering på en arbetsyta med namnet *contosoretail it*. 

Identifiera en arbetsyta kan vara på flera olika sätt:

* Resursnamnet - är ett läsbart namn på arbetsytan, kallas ibland *komponentnamn*. 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >Identifierar en arbetsyta med namnet förutsätter unikhet för alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet kan misslyckas på grund av tvetydighet. I det här fallet måste du använda en av de andra identifierarna.

* Kvalificerade namn – är ”fullständiga namn” på arbetsytan, består av prenumeration, resursgrupp och Komponentnamn i följande format: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unikt, kan den här identifieraren vara tvetydiga. 
    >

* Arbetsyte-ID - arbetsyte-ID är unikt, inte kan ändras, identifieraren som tilldelats varje arbetsyta som representeras som en globalt unik identifierare (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure resurs-ID – Azure-definierade unika identiteten för arbetsytan. Du använder resurs-ID när resursnamnet är tvetydig.  Formatet för arbetsytor, är: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. ComponentName-OperationalInsights/arbetsytor*.  

    Exempel:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identifiera ett program
I följande exempel returneras ett sammanfattande antal förfrågningar mot en app med namnet *fabrikamapp* i Application Insights. 

Identifiera ett program i Application Insights kan åstadkommas med den *app(Identifier)* uttryck.  Den *identifierare* argumentet anger appen med något av följande:

* Resursnamnet - är en mänsklig läsbart namn på appen, kallas ibland den *komponentnamn*.  

    `app("fabrikamapp")`

* Kvalificerade namnet - heter ”fullständig” appen, består av prenumeration, resursgrupp och Komponentnamn i följande format: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unikt, kan den här identifieraren vara tvetydiga. 
    >

* ID - appens GUID för programmet.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure resurs-ID - Azure-definierade unika identiteten för appen. Du använder resurs-ID när resursnamnet är tvetydig. Formatet är: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. ComponentName-OperationalInsights/komponenter*.  

    Exempel:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Utför en fråga över flera resurser
Du kan fråga flera resurser från någon av resursinstanserna, det kan vara arbetsytor och appar kombineras.
    
Exempel för fråga över två arbetsytor:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Använda mellan resurser frågan för flera resurser
När du använder frågor mellan resurser för att korrelera data från flera Log Analytics och Application Insights-resurser, kan frågan bli komplicerad och svår att underhålla. Du bör använda [funktioner i Log Analytics](query-language/functions.md) att separera frågelogiken från omfånget för frågan-resurser, vilket förenklar frågestrukturen. I följande exempel visar hur du kan övervaka flera Application Insights-resurser och visualisera antal misslyckade begäranden per programnamn. 

Skapa en fråga som detta som refererar till omfattningen för Application Insights-resurser. Den `withsource= SourceApp` kommando lägger till en kolumn som anger namnet på programmet som skickas i loggen. [Spara frågan som funktionen](query-language/functions.md#create-a-function) med alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Du kan nu [Använd den här funktionen](query-language/functions.md#use-a-function) i en fråga för mellan resurser så här. Funktionens alias _applicationsScoping_ returnerar unionen av tabellen ”requests” från alla definierade program. Frågan och sedan filtrerar för misslyckade förfrågningar och hjälper dig att visualisera trender av program. Den _parsa_ operatorn är valfria i det här exemplet. Det extraherar programnamnet från _SourceApp_ egenskapen.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| sort by count_ desc 
| render timechart
```
![tidsdiagram](media/log-analytics-cross-workspace-search/chart.png)

## <a name="next-steps"></a>Nästa steg

Granska den [Log Analytics logga sökreferens](https://docs.microsoft.com/azure/log-analytics/query-language/kusto) att visa alla fråga Syntaxalternativ som är tillgängliga i Log Analytics.    
