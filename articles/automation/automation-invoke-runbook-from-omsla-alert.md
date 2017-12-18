---
title: "Anropa en Azure Automation Runbook från en Log Analytics-avisering |Microsoft Docs"
description: "Den här artikeln innehåller en översikt över hur du anropar en Automation-runbook från en Microsoft OMS-avisering i Log Analytics."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
ms.openlocfilehash: 0c0b15f33a177afc70a3662c5bd008eb236ed0d6
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Anropa en Azure Automation-runbook från en OMS Log Analytics-avisering

Om en avisering har konfigurerats i Log Analytics för att skapa en aviseringspost om ett resultat matchar ett villkor, till exempel en långvarig topp i processorbelastningen eller en programprocess som är kritisk för funktionaliteten i ett affärsprogram misslyckas och skriver motsvarande händelse i Windows-händelseloggen, kan den aviseringen automatiskt köra en Automation-runbook för att försöka reparera problemet automatiskt.  

Det finns två alternativ för att anropa en runbook i aviseringskonfigurationen, t.ex.:

1. med en webhook.
   * Det är det enda tillgängliga alternativet om OMS-arbetsytan inte är länkad till ett Automation-konto.
   * Om du redan har ett Automation-konto som är kopplat till en OMS-arbetsyta är det här alternativet fortfarande tillgängligt.  

2. Välj en runbook direkt.
   * Det här alternativet är endast tillgängligt om OMS-arbetsytan är länkad till ett Automation-konto.

## <a name="calling-a-runbook-using-a-webhook"></a>Anropa en runbook med en webhook

Med en webhook kan du starta en viss runbook i Azure Automation via en HTTP-begäran. Innan du konfigurerar [Log Analytics-aviseringen](../log-analytics/log-analytics-alerts.md#alert-rules) så att den anropar en runbook med en webhook som en aviseringsåtgärd behöver du först skapa en webhook för den runbook som ska anropas med den här metoden. Utför stegen i artikeln om att [skapa en webhook](automation-webhooks.md#creating-a-webhook) och kom ihåg att registrera webhooksadressen så att du kan använda den när du konfigurerar aviseringsregeln.   

## <a name="calling-a-runbook-directly"></a>Anropa en runbook direkt

Om du har erbjudandet Automatisering och kontroll installerat och konfigurerat i OMS-arbetsytan när du konfigurerar alternativen för Runbook-åtgärder för aviseringen kan du visa alla runbooks i listrutan **Välj en runbook** och välja en specifik runbook du vill köra som svar på aviseringen. Den valda runbooken kan köras i en arbetsyta i Azure-molnet eller på en Hybrid Runbook Worker. När aviseringen har skapats med runbook-alternativet skapas en webhook för runbooken. Du kan se webhooken om du går till Automation-kontot och navigerar till webhookfönstret för den markerade runbooken. Om du tar bort aviseringen tas webhooken inte bort, men användaren kan ta bort webhooken manuellt. Det är inget problem om webhooken inte tas bort, det är bara ett överblivet objekt som så småningom måste tas bort för att hålla Automation-kontot i ordning.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Egenskaper för en runbook (för båda alternativen)

De båda metoderna för att anropa runbook-flödet från Log Analytics-aviseringen har egenskaper som du måste förstå innan du konfigurerar dina aviseringsregler. Aviseringsdata är i json-format i en enskild egenskap med namnet **SearchResult**. Det här formatet är till för runbook- och webhook-åtgärder med en standardnyttolast. För webhook-åtgärder med anpassade nyttolaster som inkluderar **IncludeSearchResults:True** i **RequestBody** är egenskapen **SearchResults**.

* Du måste ha en runbook-indataparameter med namnet **WebhookData** som är **objekttypen**. Det kan vara obligatoriskt eller valfritt. Aviseringen skickar sökresultaten till runbooken med denna indataparameter.

    ```powershell
    param  
        (  
        [Parameter (Mandatory=$true)]  
        [object] $WebhookData  
        )
    ```
*  Du måste ha koden för att konvertera WebhookData till ett PowerShell-objekt.

    ```powershell
    $SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
    ```

    *$SearchResult* är en matris med objekt, och varje objekt innehåller fälten med värden från ett sökresultat


## <a name="example-walkthrough"></a>Exempelgenomgång

Vi visar hur den här processen fungerar med hjälp av följande exempel på ett grafiskt runbook-flöde, som startar en Windows-tjänst.<br><br> ![Starta Windows-tjänsten grafisk Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

Runbook-flödet har en indataparameter av typen **objekt** som kallas **WebhookData** och som innehåller de webhookdata som skickas från aviseringen som innehåller \*.SearchResult\*.<br><br> ![Indataparametrar för Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

För det här exemplet skapade vi två anpassade fält i Log Analytics, \*SvcDisplayName\_CF\* och \*SvcState\_CF\*, för att extrahera tjänstens visningsnamn och status för tjänsten (d.v.s. körs eller är stoppad) från den händelse som skrevs till systemets händelselogg. Vi skapar sedan en aviseringsregel med sökvillkoret `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` så att vi kan identifiera när utskriftshanteraren stoppas på Windows-systemet. Det kan vara valfri intressant tjänst, men för det här exemplet vi refererat till en av de befintliga tjänster som ingår i Windows-operativsystemet. Aviseringsåtgärden är konfigurerad för att köra vår runbook som används i det här exemplet och köras på den Hybrid Runbook Worker som är aktiverad på målsystemet.   

Runbook-kodaktiviteten **Get Service Name from LA** (Hämta servicenamn från LA) konverterar den JSON-formaterade strängen till en objekttyp och filtrerar på objektet \*SvcDisplayName\_CF\* för att extrahera visningsnamnet för Windows-tjänsten och skicka det här värdet till nästa aktivitet som kontrollerar att tjänsten stoppas innan du försöker starta om den. *SvcDisplayName_CF* är ett [anpassat fält](../log-analytics/log-analytics-custom-fields.md) som skapas i Log Analytics för att visa det här exemplet.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

När tjänsten stoppas identifierar aviseringsregeln i Log Analytics en matchning och utlöser runbooken och skickar aviseringssammanhanget till runbook. Runbook vidtar åtgärder för att kontrollera att tjänsten har stoppats, och om så är fallet försöka starta om tjänsten och kontrollera att den startade korrekt och matar ut resultaten.     

Om ditt Automation-konto inte är länkat till OMS-arbetsytan kan du konfigurera aviseringsregeln med en webhook-åtgärd för att utlösa runbook och konfigurera runbooken så att den konverterar den JSON-formaterade strängen och filtrerar på \*.SearchResult\* enligt de riktlinjer som beskrivs ovan.    

>[!NOTE]
> Om din arbetsyta har uppgraderats till [det nya Log Analytics-frågespråket](../log-analytics/log-analytics-log-search-upgrade.md) har webhook-nyttolasten ändrats.  Mer information om formatet finns i [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse).

## <a name="next-steps"></a>Nästa steg

* Läs mer om aviseringar i Log Analytics och hur du skapar en i [Aviseringar i Log Analytics](../log-analytics/log-analytics-alerts.md).

* Information om hur du utlöser runbooks med en webhook finns [Azure Automation webhooks](automation-webhooks.md) (Azure Automation-webhookar).
