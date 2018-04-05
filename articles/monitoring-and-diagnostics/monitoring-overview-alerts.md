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
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 06ba05f71cf1f696033099c04448526a0421ad42
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Vad är klassiska aviseringar i Microsoft Azure?

> [!NOTE]
> Den här artikeln beskriver hur du skapar äldre klassiska mått aviseringar. Stöd för Azure övervakaren [nyare nära realtid mått aviseringar](monitoring-overview-unified-alerts.md)
>

Den här artikeln beskriver de olika källorna av aviseringar i Microsoft Azure, vilka syften för dessa aviseringar, sina fördelar och hur du kommer igång med att använda dem. Det gäller specifikt för klassiska Azure-Monitor-aviseringar. Aviseringar erbjuder en metod för att övervaka i Azure som gör att du kan konfigurera villkor över data och bli meddelad när villkoren matchas senaste övervakningsdata.


## <a name="taxonomy-of-azure-monitor-classic-alerts"></a>Taxonomi för klassiska Azure-Monitor-aviseringar
Azure använder följande termer för att beskriva klassiska aviseringar och deras funktioner:
* **Varning** -en definition av kriterier (en eller flera regler eller villkor) som blir aktiveras när uppfylls.
* **Aktiva** -läget när en klassiska avisering villkoren är uppfyllda.
* **Matcha** -läget när en klassiska avisering villkoren uppfylls inte längre efter tidigare är uppfyllda.
* **Meddelande** - åtgärd baserat ut från en klassiska avisering aktiveras.
* **Åtgärden** -ett specifikt anrop som skickas till en mottagare av ett meddelande (till exempel e-posta en adress eller bokföring till en Webhooksadressen). Meddelanden kan vanligtvis utlösa flera åtgärder.


## <a name="alerts-on-azure-monitor-data"></a>Aviseringar på Azure-Monitor data
Det finns tre typer av aviseringar från data från Azure-Monitor--mått aviseringar, nära realtid mått aviseringar och aktivitetsloggen aviseringar.

* **Klassiska mått aviseringar** -den här aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Aviseringen genererar ett meddelande när aviseringen är ”aktiverad” (när tröskelvärdet skärs och avisering villkoret är uppfyllt) som när den är ”löst” (när tröskelvärdet skärs igen och den är inte längre uppfyllt). Dessa är äldre mått aviseringar. Nyare mått aviseringar finns nedan.

* **Nära realtid mått aviseringar** (nyare avisering experience) - det här är nyare mått aviseringar med förbättrade funktioner jämfört med föregående mått aviseringar. Dessa aviseringar kan köras med en frekvens som 1 minut. De också stöd för övervakning av flera (för närvarande två) mått.  Aviseringen genererar ett meddelande när aviseringen är ”aktiverad” (när tröskelvärden för varje mått passeras samtidigt och avisering villkoret är uppfyllt) samt när ”löses” (när minst en måttet överskrider tröskelvärdet igen och villkoret är ingen längre uppfyllt).

* **Klassiska aktivitet loggen aviseringar** -en strömmande log-avisering som utlöses när en aktivitetsloggen-händelse genereras som matchar filtervillkor som du har tilldelat. Dessa aviseringar har endast en tillstånd ”aktiverad” eftersom aviseringen motorn gäller bara filtervillkoren för alla nya händelser. Dessa aviseringar kan användas för att bli meddelad när en ny incident tjänstens hälsa inträffar eller när en användare eller program utför en åtgärd i din prenumeration, till exempel ”ta bort virtuell dator”.

Diagnostiska loggdata som är tillgängliga via Azure-Monitor föreslår vi dirigera datan till logganalys och använder en Log Analytics-avisering. Följande diagram sammanfattar datakällor för data i Azure-Monitor och, begreppsmässigt, hur du kan ge ut från dessa data.

![Aviseringar som förklaras](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-classic-alert"></a>Hur får ett meddelande på en klassisk Azure-övervakningsavisering?
Tidigare använda Azure aviseringar från olika tjänster sina egna inbyggda metoder. Azure-Monitor erbjuder framöver, en återanvändbara notification gruppering som kallas åtgärdsgrupper. Åtgärdsgrupper ange mottagare för ett meddelande--alla antal e-postadresser, telefonnummer (för SMS) eller webhook webbadresser-- och när en avisering aktiveras som refererar till gruppen åtgärden alla mottagare få detta meddelande. På så sätt kan du återanvända en gruppering av mottagarna (till exempel på anrop tekniker listan) över många objekt som aviseringen. För närvarande bara aktivitetsloggen aviseringar genom att utnyttja åtgärdsgrupper, men flera andra Azure aviseringstyper arbetar med hjälp av åtgärdsgrupper samt.

Åtgärdsgrupper har stöd för meddelanden genom att publicera till en Webhooksadressen förutom e-postadresser och SMS-nummer. Detta kan till exempel automation och reparationer med:
    - Azure Automation – Runbook
    - Azure Function
    - Azure Logic App
    - en tjänst från tredje part

Nära realtid mått aviseringar och aktivitetsloggen aviseringar kan du använda grupper för åtgärden.

Äldre mått aviseringar under aviseringar (klassisk) använda inte grupper för åtgärden. Du kan konfigurera meddelanden till på en enskild mått varning:
* Skicka e-postmeddelanden till tjänsteadministratören, medadministratörer eller ytterligare e-postadresser som du anger.
* Anropa webhook, där du kan starta ytterligare automation-åtgärder.

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
* Lär dig mer om [nyare mått aviseringar](monitoring-near-real-time-metric-alerts.md)
* Lär dig mer om [tjänstmeddelanden](monitoring-service-notifications.md)
* Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md)
* Konfigurera [aviseringar](monitor-alerts-unified-usage.md)
