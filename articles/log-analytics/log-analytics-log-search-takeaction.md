---
title: "Användarinitierad Azure Automation Runbook-åtgärden i logganalys | Microsoft Docs"
description: "Den här artikeln beskriver hur du kör en Automation-runbook från en logganalys Sök resultat på begäran."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: b3c3b036a8294e17aec103ba470402c1f8f707d8
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Utför en åtgärd med en Automation-Runbook från ett sökresultat för logganalys-logg

Loggen sökningen i Azure Log Analytics, du kan nu välja **åtgärda** att köra en Automation-runbook.  Runbook kan användas för att åtgärda problemet eller vidta andra åtgärder som samlar in information om felsökning, skicka ett e-postmeddelande eller skapar en tjänstbegäran. 

## <a name="components-and-features-used"></a>Komponenter och funktioner som används
* [Azure Automation-konto](../automation/automation-offering-get-started.md)
* [Log Analytics-arbetsyta](../log-analytics/log-analytics-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Att starta runbook från loggen sökning

Om du vill vidta åtgärder för en händelse och startar en runbook i sökresultaten loggen, börja med att skapa en logg-sökning och du kan anropa en runbook på begäran från resultaten.  Du kan göra detta från loggen sökfunktionen i den [Azure-portalen](../log-analytics/log-analytics-log-search-new.md).  I det här exemplet söka vi loggen från Azure-portalen med en enkel demonstration av den här funktionen.

1. I Azure-portalen på navmenyn klickar du på **fler tjänster** och välj **logganalys**.  
2. I bladet logganalys väljer logganalys-arbetsytan och i bladet arbetsytan väljer **loggen Sök**.  
3. I bladet loggen Search söka loggen.  
4. I sökresultaten logg klickar du på ellipsen till vänster om något av fälten och på snabbmenyn, väljer **vidta åtgärder för**.<br><br> ![Välj Ta åtgärd sökresultat](./media/log-analytics-log-search-takeaction/log-search-takeaction-menuoption.png) 
5. Välj bladet vidta åtgärden **köra en runbook**, och på den **köra en runbook** bladet kan du välja en runbook ska köras.  Du kan välja valfri runbook i Automation-kontot som är kopplad till logganalys-arbetsytan.  Observera följande:

    * Runbooks är ordnade efter taggar
    * Runbook Indataparametern värden kan väljas direkt från fälten i sökresultatet.  En listrutan visas alla tillgängliga fält från resultatet att välja från.  
    * Du kan välja att köra runbook en [runbook worker-hybrid](../automation/automation-hybrid-runbook-worker.md) att du har installerat på den dator som har problemet om du har en motsvarande Hybrid Runbook Worker-grupp som endast innehåller den här datorn som en medlem.  Om namnet på Hybrid Worker-grupp matchar namnet på den dator som är i sökresultatet loggen, väljs gruppen automatiskt.    

6. När du klickar på **kör**, öppnas bladet för runbook-jobb så att du kan granska status för jobbet.   

Om du väljer en runbook som har konfigurerats för att vara [anropas från en avisering om logganalys](../automation/automation-invoke-runbook-from-omsla-alert.md), den har en indataparameter kallas **WebhookData** som **objekt** typen.  Om parametern är obligatorisk, måste du skicka sökresultaten till runbook så att den kan konvertera JSON-formaterad sträng till en objekttyp som gör att du kan filtrera efter specifika objekt som du sedan hänvisar till i runbook-aktiviteter.  Det gör du genom att välja **sökresultatet (objekt)** från den nedrullningsbara listan.<br><br> ![Välj Webhook-dataobjekt för runbook-parameter](media/log-analytics-log-search-takeaction/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Nästa steg

* Granska de [logganalys logga Sök referens](log-analytics-search-reference.md) att visa alla sökfält och aspekter som är tillgängliga i logganalys.
* Om du vill lära dig mer om att anropa en Automation-runbook automatiskt, granska [anropa en Azure Automation-runbook från en avisering om logganalys](../automation/automation-invoke-runbook-from-omsla-alert.md).  