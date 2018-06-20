---
title: Anropa en Azure Automation-runbook från en Log Analytics-avisering
description: Den här artikeln innehåller en översikt över hur att anropa en Automation-runbook från en avisering om logganalys i Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/12/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0094362ee083511b05027f22b37ed62d56d68d41
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217144"
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>Anropa en Azure Automation-runbook från en Log Analytics-avisering

Du kan konfigurera en avisering i Azure Log Analytics för att skapa en aviseringspost när resultaten matchar dina kriterier. Den här aviseringen kan sedan automatiskt köra en Azure Automation-runbook i ett försök att åtgärda problemet automatiskt. 

En varning kan till exempel visa en långvarig topp i processorbelastning. Eller också kan den tyda på att ett program som är viktigt för funktionen i ett affärsprogram inte fungerar. En runbook kan sedan skriva en motsvarande händelse i händelseloggen i Windows.  

Det finns två alternativ för att anropa en runbook i aviseringskonfigurationen:

* Använd en webhook.
   * Detta är det enda tillgängliga alternativet om logganalys-arbetsytan inte är länkat till ett Automation-konto.
   * Det här alternativet är fortfarande tillgänglig om du redan har ett Automation-konto som är kopplad till logganalys-arbetsytan.  

* Välj en runbook direkt.
   * Det här alternativet är bara tillgängligt om logganalys-arbetsytan är kopplad till ett Automation-konto.

## <a name="calling-a-runbook-by-using-a-webhook"></a>Anropa en runbook med en webhook

Du kan använda en webhook för att starta en viss runbook i Azure Automation via en HTTP-begäran. Innan du konfigurerar den [Webhook åtgärd för log aviseringar](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md) för att anropa runbook genom att använda en webhook som en åtgärd, behöver du [skapa en webhook](automation-webhooks.md#creating-a-webhook) för alla runbooks som anropas via den här metoden. Kom ihåg att registrera Webhookens URL så att du kan referera till den när varningsregeln konfigureras.   

## <a name="calling-a-runbook-directly"></a>Anropa en runbook direkt

Du kan installera och konfigurera automatisering och kontroll erbjuder i logganalys-arbetsytan. När du konfigurerar alternativ för runbook-åtgärder för aviseringen, kan du visa alla runbooks från listrutan **Välj en runbook** och välj den specifika runbook som du vill köra som svar på aviseringen. Den valda runbooken kan köras i en Azure-arbetsyta eller på en Hybrid Runbook Worker. 

När du har skapat aviseringen med hjälp av runbook-alternativet skapas en webhook för runbooken. Du kan se webhooken om du går till Automation-kontot och öppnar webhookfönstret för den markerade runbooken. 

Om du tar bort aviseringen tas inte webhooken bort. Detta är inte ett problem. Webhooken blir bara ett överblivet objekt som du slutligen kan ta bort manuellt, för att upprätthålla ett ordnat Automation-konto.  

## <a name="characteristics-of-a-runbook"></a>Egenskaper för en runbook

De båda metoderna för att anropa runbook-flödet från Log Analytics-aviseringen har egenskaper som du måste förstå innan du konfigurerar dina aviseringsregler. 

Aviseringsdata är i JSON-format i en enskild egenskap med namnet **SearchResult**. Det här formatet är till för runbook- och webhook-åtgärder med en standardnyttolast. För webhook-åtgärder med anpassade nyttolaster som inkluderar **IncludeSearchResults:True** i **RequestBody** är egenskapen **SearchResults**.

Du måste ha en runbook-indataparameter med namnet **WebhookData** som är en **objekttyp**. Det kan vara obligatoriskt eller valfritt. Aviseringen skickar sökresultaten till runbooken med denna indataparameter.

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
Du måste också ha koden för att konvertera **WebhookData** till ett PowerShell-objekt.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

**$SearchResult** är en matris av objekt. Varje objekt innehåller fält med värden från ett sökresultat.


## <a name="example-walkthrough"></a>Exempelgenomgång

Följande exempel av en grafisk runbook visar hur den här processen fungerar. Den startar en Windows-tjänst.

![Grafisk runbook för att starta en Windows-tjänst](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

Runbook har en indataparameter av typen **objekt** som kallas **WebhookData**. Den innehåller webhook-data som skickas från den avisering som innehåller **SearchResult**.

![Indataparametrar för Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

I det här exemplet skapade vi två anpassade fält i Log Analytics: **SvcDisplayName_CF** och **SvcState_CF**. De här fälten extraherar visningsnamn för tjänsten och status för tjänsten (som är igång eller stoppad) från den händelse som skrivs till systemhändelseloggen. Vi skapar sedan en aviseringsregel med följande sökvillkor så att vi kan identifiera när utskriftshanteraren stoppas på Windows-systemet:

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

Det kan vara någon tjänst av intresse. I det här exemplet refererar vi till en av de befintliga tjänsterna som ingår i Windows-operativsystemet. Aviseringsåtgärden är konfigurerad för att köra den runbook som används i det här exemplet och köra den på den Hybrid Runbook Worker som är aktiverad på målsystemet.   

Runbook-kodaktiviteten **Hämta tjänstnamn från LA** konverterar den JSON-formaterade strängen till en objekttyp och filtrerar på objektet **SvcDisplayName_CF**. Den extraherar visningsnamn för Windows-tjänsten och skickar det här värdet till nästa aktivitet, vilken verifierar att tjänsten stoppas innan du försöker starta om den. **SvcDisplayName_CF** är ett [anpassat fält](../log-analytics/log-analytics-custom-fields.md) som vi har skapat i Log Analytics för att visa det här exemplet.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

När tjänsten stoppas identifierar aviseringsregeln i Log Analytics en matchning, utlöser runbooken och skickar aviseringssammanhanget till runbook. Runbook försöker verifiera att tjänsten har stoppats. I så fall, försöker runbook att starta om tjänsten, kontrollera att den startade korrekt och visa resultaten.     

Om du inte har ditt Automation-konto som är kopplad till logganalys-arbetsytan kan konfigurera du också varningsregeln med en webhook-åtgärd. Webhook-åtgärden utlöser runbooken. Den konfigurerar också runbooken för att konvertera den JSON-formaterade strängen och filtrera på **SearchResult** genom att följa anvisningarna ovan.    

>[!NOTE]
> Om din arbetsyta har uppgraderats till [det nya Log Analytics-frågespråket](../log-analytics/log-analytics-log-search-upgrade.md) har webhook-nyttolasten ändrats. Mer information om formatet finns i [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar ett Azure avisering med hjälp av en logg sökning finns [Logga varningar i Azure](../monitoring-and-diagnostics/monitor-alerts-unified-log.md).

* Information om hur du utlöser runbooks med en webhook finns [Azure Automation-webhookar](automation-webhooks.md).
