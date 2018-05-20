---
title: Översikt över klassiska aviseringar i Microsoft Azure och Azure-Monitor | Microsoft Docs
description: Aviseringar kan du övervaka Azure-resurs mått, händelser eller loggar och aviseras när du anger villkor.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: robb
ms.openlocfilehash: 039ab7226b4ea7e011e1c0cbb4cac528b5237483
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Vad är klassiska aviseringar i Microsoft Azure?

> [!NOTE]
> Den här artikeln beskriver hur du skapar äldre klassiska mått aviseringar. Stöd för Azure övervakaren [nyare nära realtid mått aviseringar och en ny aviseringar miljö](monitoring-overview-unified-alerts.md). 
>

Aviseringar kan du konfigurera villkor över data och bli meddelad när villkoren matchas senaste övervakningsdata.


## <a name="alerts-on-azure-monitor-data"></a>Aviseringar på Azure-Monitor data
Det finns två typer av klassiska aviseringar tillgänglig - mått aviseringar och aktivitet loggen aviseringar.

* **Klassiska mått aviseringar** -den här aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Aviseringen genererar ett meddelande när aviseringen är ”aktiverad” (när tröskelvärdet skärs och avisering villkoret är uppfyllt) som när den är ”löst” (när tröskelvärdet skärs igen och den är inte längre uppfyllt). Nyare mått aviseringar finns nedan.

* **Klassiska aktivitet loggen aviseringar** -en strömmande log-avisering som utlöses när en aktivitetsloggen-händelse genereras som matchar filtervillkor som du har tilldelat. Dessa aviseringar har endast en tillstånd ”aktiverad” eftersom aviseringen motorn gäller bara filtervillkoren för alla nya händelser. Dessa aviseringar kan användas för att bli meddelad när en ny incident tjänstens hälsa inträffar eller när en användare eller program utför en åtgärd i din prenumeration, till exempel ”ta bort virtuell dator”.

Diagnostiska loggdata som är tillgängliga via Azure-Monitor föreslår vi dirigera datan till logganalys (tidigare OMS) och använder en avisering om logganalys-fråga. Logga Analytics nu använder den [nya aviseringar metod](monitoring-overview-unified-alerts.md) 

Följande diagram sammanfattar datakällor för data i Azure-Monitor och, begreppsmässigt, hur du kan ge ut från dessa data.

![Aviseringar som förklaras](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taxonomi för Azure övervaka aviseringar (klassisk)
Azure använder följande termer för att beskriva klassiska aviseringar och deras funktioner:
* **Varning** -en definition av kriterier (en eller flera regler eller villkor) som blir aktiveras när uppfylls.
* **Aktiva** -läget när en avisering om klassiska villkoren är uppfyllda.
* **Matcha** -läget när en avisering om klassiska villkoren uppfylls inte längre efter tidigare är uppfyllda.
* **Meddelande** - åtgärd baserat ut från en klassiska avisering aktiveras.
* **Åtgärden** -ett specifikt anrop som skickas till en mottagare av ett meddelande (till exempel e-posta en adress eller bokföring till en Webhooksadressen). Meddelanden kan vanligtvis utlösa flera åtgärder.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Hur tar emot ett meddelande från en klassiska Azure-övervakningsavisering?
Tidigare använda Azure aviseringar från olika tjänster sina egna inbyggda metoder. 

Nu Azure ger ett återanvändbara meddelande gruppering kallas *åtgärdsgrupper*. Åtgärdsgrupper ange mottagare för ett meddelande och när en avisering aktiveras som refererar till gruppen åtgärden alla mottagare få detta meddelande. På så sätt kan du återanvända en gruppering av mottagarna (till exempel på anrop tekniker listan) över många objekt som aviseringen. Åtgärdsgrupper har stöd för meddelanden genom att publicera till en Webhooksadressen förutom e-postadresser, SMS-nummer och ett antal andra åtgärder.  Mer information finns i [åtgärdsgrupper](monitoring-action-groups.md). 

Äldre klassiska aktivitetsloggen aviseringar använda grupper för åtgärden.

Äldre mått aviseringar du inte använda grupper för åtgärden. I stället kan du konfigurera följande åtgärder: 
* Skicka e-postmeddelanden till tjänsteadministratören, medadministratörer eller ytterligare e-postadresser som du anger.
* Anropa webhook, där du kan starta ytterligare automation-åtgärder.

Webhooks kan automation- och reparationsloggarna, till exempel med hjälp av:
    - Azure Automation – Runbook
    - Azure-funktion
    - Azure Logikapp
    - en tjänst från tredje part

## <a name="next-steps"></a>Nästa steg
Hämta information om Varningsregler och konfigurerar dem med hjälp av:

* Lär dig mer om [mått](monitoring-overview-metrics.md)
* Konfigurera [klassiska mått aviseringar via Azure-portalen](insights-alerts-portal.md)
* Konfigurera [klassiska mått aviseringar PowerShell](insights-alerts-powershell.md)
* Konfigurera [klassiska mått aviseringar kommandoradsgränssnittet (CLI)](insights-alerts-command-line-interface.md)
* Konfigurera [klassiska mått aviseringar Azure övervakaren REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Lär dig mer om [aktivitetsloggen](monitoring-overview-activity-logs.md)
* Konfigurera [aktivitet loggen aviseringar via Azure-portalen](monitoring-activity-log-alerts.md)
* Konfigurera [aktivitet loggen aviseringar via Hanteraren för filserverresurser](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Granska de [avisering webhook för aktivitetslogg](monitoring-activity-log-alerts-webhook.md)
* Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md)
* Konfigurera [nyare aviseringar](monitor-alerts-unified-usage.md)
