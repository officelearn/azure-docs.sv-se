---
title: "Anropa en Azure Automation Runbook från en Log Analytics-avisering |Microsoft Docs"
description: "Den här artikeln innehåller en översikt över hur du anropar en Automation-runbook från en Microsoft OMS-avisering i Log Analytics."
services: automation
documentationcenter: 
author: mgoedtel
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
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 81cf490eae7f283c0180875cb3a2ed2ffe6333c8
ms.lasthandoff: 03/29/2017

---

# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Anropa en Azure Automation-runbook från en OMS Log Analytics-avisering

När en avisering har konfigurerats i Log Analytics för att skapa en aviseringspost om ett resultat matchar ett visst villkor, till exempel en långvarig topp i processorbelastningen eller en viss programprocess som är kritisk för funktionaliteten i ett affärsprogram misslyckas och skriver motsvarande händelse i Windows-händelseloggen, kan den aviseringen automatiskt köra en Automation-runbook för att försöka reparera problemet automatiskt.  

Det finns två alternativ för att anropa en runbook när du konfigurerar aviseringen.  Mer specifikt:

1. med en webhook.
   * Detta är det enda tillgängliga alternativet om OMS-arbetsytan inte är länkad till ett Automation-konto.
   * Om du redan har ett Automation-konto som är kopplat till en OMS-arbetsyta är det här alternativet fortfarande tillgängligt.  

2. Välj en runbook direkt.
   * Det här alternativet är endast tillgängligt när OMS-arbetsytan är länkad till ett Automation-konto.  

## <a name="calling-a-runbook-using-a-webhook"></a>Anropa en runbook med en webhook

Med en webhook kan du starta en viss runbook i Azure Automation via en HTTP-begäran.  Innan du konfigurerar [Log Analytics-aviseringen](../log-analytics/log-analytics-alerts.md#alert-rules) så att den anropar en runbook med en webhook som en aviseringsåtgärd behöver du först skapa en webhook för den runbook som ska anropas med den här metoden.  Granska och följ stegen i artikeln [om att skapa en webhook](automation-webhooks.md#creating-a-webhook) och kom ihåg att registrera webhooksadressen så att du kan använda den när du konfigurerar aviseringsregeln.   

## <a name="calling-a-runbook-directly"></a>Anropa en runbook direkt

Om du har erbjudandet Automation & Control (automatisering och kontroll) installerat och konfigurerat i OMS-arbetsytan när du konfigurerar alternativen för Runbook-åtgärder för aviseringen kan du visa alla runbooks på listrutan **Select a runbook** (Välj en runbook) och välja en specifik runbook du vill köra som svar på aviseringen.  Den valda runbooken kan köras i en arbetsyta i Azure-molnet eller på en Hybrid Runbook Worker.  När aviseringen har skapats med alternativet runbook skapas en webhook för runbooken.  Du kan se webhooken om du går till Automation-kontot och navigerar till webhookbladet för den markerade runbooken.  Om du tar bort aviseringen tas webhooken inte bort, men användaren kan ta bort webhooken manuellt.  Det är inget problem om webhooken inte tas bort, det är bara ett överblivet objekt som så småningom måste tas bort för att hålla Automation-kontot i ordning.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Egenskaper för en runbook (för båda alternativen)

De båda metoderna för att anropa runbooken från Log Analytics-aviseringen har olika egenskaper som du måste förstå innan du konfigurerar dina aviseringsregler.  

* Du måste ha en runbook-indataparameter med namnet **WebhookData** som är **objekttypen**.  Det kan vara obligatoriskt eller valfritt.  Aviseringen skickar sökresultaten till runbooken med denna indataparameter.

        param  
         (  
          [Parameter (Mandatory=$true)]  
          [object] $WebhookData  
         )

*  Du måste ha koden för att konvertera WebhookData till ett PowerShell-objekt.

    `$SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value`

    *$SearchResults* kommer att vara en matris med objekt, och varje objekt innehåller fälten med värden från ett sökresultat

### <a name="webhookdata-inconsistencies-between-the-webhook-option-and-runbook-option"></a>WebhookData-inkonsekvenser mellan webhook-alternativet och runbook-alternativet

* När du konfigurerar en avisering om att anropa en Webhook ska du ange webhooksadressen som du skapade för en runbook och klicka på knappen **Test Webhook** (Testa Webhook).  Resulterande WebhookData som skickas till runbooken innehåller varken *.SearchResult* eller *.SearchResults*.

*  Om du sparar den aviseringen innehåller WebhookData som skickas till runbooken *.SearchResult* när aviseringen utlöses och anropar webhooken.
* Om du skapar en avisering och konfigurerar den för att anropa en runbook (som även skapar en webhook) skickar den WebhookData till en runbook som innehåller *.SearchResults* när en avisering utlöses.

Därför måste du i ovanstående kodexempel hämta *.SearchResult* om aviseringen anropar en webhook, och du måste hämta *.SearchResults* om aviseringen anropar en runbook direkt.

## <a name="example-walkthrough"></a>Exempelgenomgång

Vi visar hur det fungerar med hjälp av följande exempel på en grafisk runbook, som startar en Windows-tjänst.<br><br> ![Starta Windows-tjänsten grafisk Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

Runbooken har en indataparameter av typen **objekt** som kallas **WebhookData** och innehåller de webhookdata som skickas från aviseringen som innehåller *.SearchResults*.<br><br> ![Indataparametrar för Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

För det här exemplet skapade vi i Log Analytics två anpassade fält, *SvcDisplayName_CF* och *SvcState_CF*, för att extrahera tjänstens visningsnamn och status för tjänsten (d.v.s. körs eller är stoppad) från den händelse som skrevs till systemets händelselogg.  Vi skapar sedan en aviseringsregel med sökvillkoret `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` så att vi kan identifiera när utskriftshanteraren stoppas på Windows-systemet.  Det kan vara valfri intressant tjänst, men för det här exemplet vi refererat till en av de befintliga tjänster som ingår i Windows-operativsystemet.  Aviseringsåtgärden är konfigurerad för att köra vår runbook som används i det här exemplet och köras på den Hybrid Runbook Worker som är aktiverad på målsystemen.   

Runbookkod-aktiviteten **Get Service Name from LA** (Hämta servicenamn från LA) konverterar den JSON-formaterade strängen till en objekttyp och filtrerar på objektet *SvcDisplayName_CF* för att extrahera visningsnamnet för Windows-tjänsten och skicka det till nästa aktivitet som kontrollerar att tjänsten stoppas innan du försöker starta om den.  *SvcDisplayName_CF* är ett [anpassat fält](../log-analytics/log-analytics-custom-fields.md) som skapas i Log Analytics för att visa det här exemplet.

    $SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value
    $SearchResults.SvcDisplayName_CF  

När tjänsten stoppas identifierar aviseringsregeln i Log Analytics en matchning och utlöser runbooken och skickar aviseringssammanhanget till runbook. Runbook ska vidta åtgärder för att kontrollera att tjänsten har stoppats, och om så är fallet försöka starta om tjänsten och kontrollera att den startade korrekt och matar ut resultaten.     

Om ditt Automation-konto inte är länkat till OMS-arbetsytan kan du konfigurera aviseringsregeln med en webhook-åtgärd för att utlösa runbook och konfigurera runbooken så att den konverterar den JSON-formaterade strängen och filtrerar på *.SearchResult* enligt de riktlinjer som beskrivs ovan.    

## <a name="next-steps"></a>Nästa steg

* Läs mer om aviseringar i Log Analytics och hur du skapar en i [Aviseringar i Log Analytics](../log-analytics/log-analytics-alerts.md).

* Information om hur du utlöser runbooks med en webhook finns [Azure Automation webhooks](automation-webhooks.md) (Azure Automation-webhookar).

