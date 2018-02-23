---
title: "Utföra frågor via Azure logganalys arbetsytor | Microsoft Docs"
description: "Den här artikeln beskriver hur du kan fråga över flera arbetsytor och viss App Insights app i din prenumeration."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: magoedte
ms.openlocfilehash: 403448995c28ff7172d2c3abbf3b9d67341017b4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-perform-queries-across-multiple-log-analytics-workspaces"></a>Hur du utför frågor över flera logganalys arbetsytor

Tidigare med Azure logganalys du kan endast analysera data från inom den aktuella arbetsytan och den begränsade möjligheten att fråga över flera arbetsytor som definierats i din prenumeration.  

Nu kan du fråga inte bara över flera logganalys arbetsytor, utan också data från en viss Application Insights-app i samma resursgrupp, en annan resursgrupp eller en annan prenumeration. Detta ger dig en systemomfattande vy över dina data.  Du kan bara utföra den här typen av fråga i den [avancerade portal](log-analytics-log-search-portals.md#advanced-analytics-portal), inte i Azure-portalen.  

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Fråga efter över logganalys arbetsytor och från Application Insights
Om du vill referera till en annan arbetsyta i frågan, Använd den [ *arbetsytan* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) identifierare, och en app från Application Insights använder den [ *app* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app())identifierare.  

Till exempel den första frågan returnerar sammanfattade antalet uppdateringar som krävs för deras klassificering från tabellen uppdateringen från både den aktuella arbetsytan och en annan arbetsyta med namnet *contosoretail it*.  Det andra exemplet i frågan returnerar sammanfattade antalet förfrågningar som görs mot en app med namnet *fabrikamapp* i Application Insights. 

### <a name="identifying-workspace-resources"></a>Identifiera resurser i arbetsytan
Identifiera en arbetsyta kan utföras på flera olika sätt:

* Resurs - är ett läsbart namn på arbetsytan, kallas ibland *komponentnamnet*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >Identifierar en arbetsyta med namnet förutsätter att det är unikt över alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet kan misslyckas på grund av tvetydighet. I det här fallet måste du använda en av de andra identifierarna.

* Kvalificerat namn - heter ”full” arbetsytan består av prenumerationen, resursgruppen och Komponentnamn i det här formatet: *komponentnamn-subscriptionName/resourceGroup*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unika kan identifieraren vara tvetydig. 
    >

* Arbetsyte-ID - arbetsyte-ID är unikt, ändras, identifierare som tilldelas varje arbetsyta som visas i form av en globalt unik identifierare (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Azure resurs-ID – Azure-definierade unik identitet i arbetsytan. Du använder resurs-ID när resursnamnet är tvetydig.  För arbetsytor, formatet är: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Komponentnamn-OperationalInsights/arbetsytor*.  

    Exempel:
    ``` 
    workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>Identifiera ett program

Identifiera ett program i Application Insights kan utföras med den *app(Identifier)* uttryck.  Den *identifierare* argumentet anger appen med något av följande:

* Resursnamnet - är en mänsklig läsbart namn på appen, som ibland kallas den *komponentnamnet*.  

    `app("fabrikamapp")`

* Kvalificerat namn - heter ”full” appen, som består av prenumerationen, resursgruppen och Komponentnamn i det här formatet: *komponentnamn-subscriptionName/resourceGroup*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unika kan identifieraren vara tvetydig. 
    >

* ID - appens GUID för programmet.

    `app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count`

* Azure resurs-ID - Azure-definierade unika identitet appen. Du använder resurs-ID när resursnamnet är tvetydig. Formatet är: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Komponentnamn-OperationalInsights/komponenter*.  

    Exempel:
    ```
    app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>Nästa steg

Granska de [logganalys logga Sök referens](https://docs.loganalytics.io/docs/Language-Reference) att visa alla frågan syntax alternativen i logganalys.    