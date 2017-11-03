---
title: "Utföra frågor via Azure logganalys arbetsytor | Microsoft Docs"
description: "Den här artikeln beskriver hur du kan fråga på flera arbetsytor i din prenumeration med exempel på att följa."
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
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>Köra frågor över flera logganalys arbetsytor

Tidigare med Azure logganalys kunde du endast analysera data inifrån den aktuella arbetsytan och den här begränsat möjligheten att fråga över flera arbetsytor som definierats i din prenumeration.  

Nu kan du fråga över flera arbetsytor att tillhandahålla en systemomfattande överblick över dina data.  Du kan bara utföra den här typen av fråga i den [avancerade portal](log-analytics-log-search-portals.md#advanced-analytics-portal), inte i Azure-portalen.  

## <a name="querying-across-log-analytics-workspaces"></a>Frågar över logganalys arbetsytor
Om du vill referera till en annan arbetsyta i frågan, Använd den *arbetsytan* identifierare.  Följande fråga returnerar till exempel sammanfattade antalet uppdateringar som krävs för deras klassificering från tabellen uppdateringen från både den aktuella arbetsytan och en annan arbetsyta med namnet *contosoretail it*.  


## <a name="identifying-resources"></a>Identifiera resurser
Identifiera en arbetsyta kan utföras på flera olika sätt:

* Resurs - är ett läsbart namn på arbetsytan, kallas ibland *komponentnamnet*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >Identifierar en arbetsyta med namnet förutsätter att det är unikt över alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet kan misslyckas på grund av tvetydighet. I det här fallet måste du använda en av de andra identifierarna.

* Kvalificerade namnet - heter ”full” i arbetsytan består av prenumerationsnamn, resursgrupp och komponenten namn i det här formatet: *komponentnamn-subscriptionName/resourceGroup*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Eftersom Azure-prenumerationsnamn inte är unika kan identifieraren vara tvetydig. 
    >

* Arbetsyte-ID - arbetsyte-ID är unikt, ändras, identifierare som tilldelas varje arbetsyta som visas i form av en globalt unik identifierare (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Azure resurs-ID – Azure-definierade unik identitet i arbetsytan. Den används när resursnamnet är tvetydig.  För arbetsytor, formatet är: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Komponentnamn-OperationalInsights/arbetsytor*.  

    Exempel:
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>Nästa steg

Granska de [logganalys logga Sök referens](https://docs.loganalytics.io/docs/Language-Reference) att visa alla frågan syntax alternativen i logganalys.    