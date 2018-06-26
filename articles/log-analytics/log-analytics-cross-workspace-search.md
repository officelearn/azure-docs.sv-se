---
title: Söka på flera resurser med Azure Log Analytics | Microsoft Docs
description: Den här artikeln beskriver hur du kan fråga mot resurser från flera arbetsytor och App Insights app i din prenumeration.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: magoedte
ms.openlocfilehash: 52c3914cc1b51bf7c2a6d0fbf28dc0bf7756e749
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751453"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Cross-resurs loggen sökning i logganalys  

Tidigare med Azure logganalys du kan endast analysera data från inom den aktuella arbetsytan och det begränsade möjligheten att fråga över flera arbetsytor som definierats i din prenumeration.  Dessutom kan du bara söka telemetri objekt som samlas in från ditt webbaserade program med Application Insights direkt i Application Insights eller från Visual Studio.  Detta också gjort det en utmaning att internt analysera operativa och programdata tillsammans.   

Nu kan du fråga inte bara över flera logganalys arbetsytor, utan också data från en viss Application Insights-app i samma resursgrupp, en annan resursgrupp eller en annan prenumeration. Detta ger dig en systemomfattande vy över dina data.  Du kan bara utföra dessa typer av frågorna i den [avancerade portal](log-analytics-log-search-portals.md#advanced-analytics-portal), inte i Azure-portalen. Antalet resurser (logganalys arbetsytor och Application Insights-app) som ska inkluderas i en enskild fråga är begränsad till 100. 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Fråga efter över logganalys arbetsytor och från Application Insights
Om du vill referera till en annan arbetsyta i frågan, Använd den [ *arbetsytan* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) identifierare, och en app från Application Insights använder den [ *app* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app())identifierare.  

### <a name="identifying-workspace-resources"></a>Identifiera resurser i arbetsytan
Följande exempel visar frågor över logganalys arbetsytor att returnera sammanfattade antal uppdateringar från tabellen uppdatering på en arbetsyta med namnet *contosoretail it*. 

Identifiera en arbetsyta kan vara utfört en av flera olika sätt:

* Resurs - är ett läsbart namn på arbetsytan, kallas ibland *komponentnamnet*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >Identifierar en arbetsyta med namnet förutsätter unikhet över alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet kan misslyckas på grund av tvetydighet. I det här fallet måste du använda en av de andra identifierarna.

* Kvalificerat namn - heter ”full” arbetsytan består av prenumerationen, resursgruppen och Komponentnamn i det här formatet: *komponentnamn-subscriptionName/resourceGroup*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unika kan identifieraren vara tvetydig. 
    >

* Arbetsyte-ID - arbetsyte-ID är unikt, ändras, identifierare som tilldelas varje arbetsyta som visas i form av en globalt unik identifierare (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure resurs-ID – Azure-definierade unik identitet i arbetsytan. Du använder resurs-ID när resursnamnet är tvetydig.  För arbetsytor, formatet är: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Komponentnamn-OperationalInsights/arbetsytor*.  

    Exempel:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>Identifiera ett program
I följande exempel returneras en sammanfattande antal begäranden som görs mot en app med namnet *fabrikamapp* i Application Insights. 

Identifiera ett program i Application Insights kan åstadkommas med den *app(Identifier)* uttryck.  Den *identifierare* argumentet anger appen med något av följande:

* Resursnamnet - är en mänsklig läsbart namn på appen, som ibland kallas den *komponentnamnet*.  

    `app("fabrikamapp")`

* Kvalificerat namn - heter ”full” appen, som består av prenumerationen, resursgruppen och Komponentnamn i det här formatet: *komponentnamn-subscriptionName/resourceGroup*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unika kan identifieraren vara tvetydig. 
    >

* ID - appens GUID för programmet.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure resurs-ID - Azure-definierade unika identitet appen. Du använder resurs-ID när resursnamnet är tvetydig. Formatet är: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Komponentnamn-OperationalInsights/komponenter*.  

    Exempel:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>Nästa steg

Granska de [logganalys logga Sök referens](https://docs.loganalytics.io/docs/Language-Reference) att visa alla frågan syntax alternativen i logganalys.    
