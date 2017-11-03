---
title: "Översikt över aviseringar i Microsoft Azure och Azure-Monitor | Microsoft Docs"
description: "Aviseringar kan du övervaka Azure-resurs mått, händelser eller loggar och aviseras när du anger villkor."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: afa863e2a900d4f823b77453d92f034db7d5a93f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-alerts-in-microsoft-azure"></a>Vad är aviseringar i Microsoft Azure?
Den här artikeln beskriver de olika källorna av aviseringar i Microsoft Azure, vilka syften för dessa aviseringar, sina fördelar och hur du kommer igång med att använda dem. Den specifikt gäller för Azure-Monitor, men ger aviseringar samt länkar till andra tjänster. Aviseringar erbjuder en metod för att övervaka i Azure som gör att du kan konfigurera villkor över data och bli meddelad när villkoren matchas senaste övervakningsdata.

## <a name="taxonomy-of-azure-alerts"></a>Taxonomi för Azure-aviseringar
Azure använder följande termer för att beskriva aviseringar och deras funktioner:
* **Varning** -en definition av kriterier (en eller flera regler eller villkor) som blir aktiveras när uppfylls.
* **Aktiva** -läget när följande villkor som definieras av en avisering är uppfyllda.
* **Matcha** -läge när de kriterier som definieras av en avisering är inte längre uppfyllt efter tidigare är uppfyllda.
* **Meddelande** - åtgärd baserat på en avisering som blir aktiv.
* **Åtgärden** -ett specifikt anrop som skickas till en mottagare av ett meddelande (till exempel e-posta en adress eller bokföring till en Webhooksadressen). Meddelanden kan vanligtvis utlösa flera åtgärder.

## <a name="alerts-in-different-azure-services"></a>Aviseringar i olika Azure-tjänster
Aviseringar är tillgänglig över flera Azure övervaka tjänster. Information om hur och när du använder dessa tjänster [finns i den här artikeln](./monitoring-overview.md). Här är en uppdelning av aviseringstyperna i Azure:

| Tjänst | Typ av avisering | Tjänster som stöds | Beskrivning |
|---|---|---|---|
| Azure Monitor | [Mått aviseringar](./insights-alerts-portal.md) | [Stöds mått från Azure-Monitor](./monitoring-supported-metrics.md) | Ett meddelande när någon plattform nivå mått uppfyller ett visst villkor (till exempel processor i procent på en virtuell dator är större än 90 för senaste 5 minuter). |
|Azure Monitor | [Nära realtid mått aviseringar (förhandsgranskning)](./monitoring-near-real-time-metric-alerts.md)| [Stöds resurser från Azure-Monitor](./monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for) | Ett meddelande snabbare än mått aviseringar när ett eller flera mått för plattformen nivå uppfyller angivna villkor (till exempel processor i procent på en virtuell dator är större än 90 och nätverket i är större än 500 MB för senaste 5 minuter). |
| Azure Monitor | [Aktiviteten loggen aviseringar](./monitoring-activity-log-alerts.md) | Alla resurstyper som är tillgängliga i Azure Resource Manager | Få en avisering när någon ny händelse i den [Azure-aktivitetsloggen](./monitoring-overview-activity-logs.md) matchar vissa villkor (till exempel när en åtgärd för ”ta bort virtuell dator” inträffar i myProductionResourceGroup eller när en ny händelse tjänstens hälsa med ”aktiv” status som visas). |
| Application Insights | [Mått aviseringar](../application-insights/app-insights-alerts.md) | Alla program som instrumenterats ska skicka data till Application Insights | Ett meddelande när någon på programnivå mått uppfyller ett visst villkor (till exempel serversvarstid är större än 2 sekunder). |
| Application Insights | [Web test varningar](../application-insights/app-insights-monitor-web-app-availability.md) | Alla webbplatser som instrumenterats ska skicka data till Application Insights | Få ett meddelande när tillgänglighet och svarstider på en webbplats som är lägre än förväntningar. |
| Log Analytics | [Log Analytics aviseringar](../log-analytics/log-analytics-alerts.md) | Alla tjänster som konfigurerats för att skicka data till logganalys | Få ett meddelande när en logganalys sökfråga över mått och/eller händelse data uppfyller vissa villkor. |

## <a name="alerts-on-azure-monitor-data"></a>Aviseringar på Azure-Monitor data
Det finns tre typer av aviseringar från data från Azure övervakaren--mått aviseringar nära realtid mått aviseringar (förhandsversion) och aktivitetsloggen aviseringar.

* **Mått aviseringar** -den här aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Aviseringen genererar ett meddelande när aviseringen är ”aktiverad” (när tröskelvärdet skärs och avisering villkoret är uppfyllt) som när den är ”löst” (när tröskelvärdet skärs igen och den är inte längre uppfyllt). En växande lista över tillgängliga mått som stöds av Azure-monitor finns [listan över mått stöds på Azure-Monitor](monitoring-supported-metrics.md).
* **Nära realtid mått aviseringar (förhandsgranskning)** - aviseringarna liknar mått aviseringar men skiljer sig på flera sätt. Det första som namnet antyder kan dessa aviseringar utlösas i nära realtid (så snabbt som 1 min.). De också stöd för övervakning av flera (för närvarande två) mått.  Aviseringen genererar ett meddelande när aviseringen är ”aktiverad” (när tröskelvärden för varje mått passeras samtidigt och avisering villkoret är uppfyllt) samt när ”löses” (när minst en måttet överskrider tröskelvärdet igen och villkoret är ingen längre uppfyllt).

> [!NOTE]
> Nära realtid måttet är aviseringar för närvarande i förhandsversion. Funktioner och användarupplevelse kan ändras.
>
>

* **Loggen Aktivitetsaviseringar** -en strömmande log-avisering som utlöses när en aktivitetsloggen-händelse genereras som matchar filtervillkor som du har tilldelat. Dessa aviseringar har endast en tillstånd ”aktiverad” eftersom aviseringen motorn gäller bara filtervillkoren för alla nya händelser. Dessa aviseringar kan användas för att bli meddelad när en ny incident tjänstens hälsa inträffar eller när en användare eller program utför en åtgärd i din prenumeration, till exempel ”ta bort virtuell dator”.

Diagnostiska loggdata som är tillgängliga via Azure-Monitor föreslår vi dirigera datan till logganalys och använder en Log Analytics-avisering. Följande diagram sammanfattar datakällor för data i Azure-Monitor och, begreppsmässigt, hur du kan ge ut från dessa data.

![Aviseringar som förklaras](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>Hur får ett meddelande på en Azure-Monitor alert?
Tidigare använda Azure aviseringar från olika tjänster sina egna inbyggda metoder. Azure-Monitor erbjuder framöver, en återanvändbara notification gruppering som kallas åtgärdsgrupper. Åtgärdsgrupper ange mottagare för ett meddelande--alla antal e-postadresser, telefonnummer (för SMS) eller webhook webbadresser-- och när en avisering aktiveras som refererar till gruppen åtgärden alla mottagare få detta meddelande. På så sätt kan du återanvända en gruppering av mottagarna (till exempel på anrop tekniker listan) över många objekt som aviseringen. För närvarande bara aktivitetsloggen aviseringar genom att utnyttja åtgärdsgrupper, men flera andra Azure aviseringstyper arbetar med hjälp av åtgärdsgrupper samt.

Åtgärdsgrupper har stöd för meddelanden genom att publicera till en Webhooksadressen förutom e-postadresser och SMS-nummer. Detta kan till exempel automation och reparationer med:
    - Azure Automation – Runbook
    - Azure-funktion
    - Azure Logikapp
    - en tjänst från tredje part

Nära realtid mått aviseringar (förhandsversion) och aktivitetsloggen aviseringar kan du använda grupper för åtgärden.

Mått aviseringar använder inte ännu åtgärdsgrupper. Du kan konfigurera meddelanden till på en enskild mått varning:
* Skicka e-postmeddelanden till tjänsteadministratören, medadministratörer eller ytterligare e-postadresser som du anger.
* Anropa webhook, där du kan starta ytterligare automation-åtgärder.

## <a name="next-steps"></a>Nästa steg
Hämta information om Varningsregler och konfigurerar dem med hjälp av:

* Lär dig mer om [mått](monitoring-overview-metrics.md)
* Konfigurera [mått aviseringar via Azure-portalen](insights-alerts-portal.md)
* Konfigurera [mått aviseringar PowerShell](insights-alerts-powershell.md)
* Konfigurera [mått aviseringar kommandoradsgränssnittet (CLI)](insights-alerts-command-line-interface.md)
* Konfigurera [mått aviseringar Azure övervakaren REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Lär dig mer om [aktivitetsloggen](monitoring-overview-activity-logs.md)
* Konfigurera [aktivitet loggen aviseringar via Azure-portalen](monitoring-activity-log-alerts.md)
* Konfigurera [aktivitet loggen aviseringar via Hanteraren för filserverresurser](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Granska de [avisering webhook för aktivitetslogg](monitoring-activity-log-alerts-webhook.md)
* Lär dig mer om [nära realtid mått aviseringar](monitoring-near-real-time-metric-alerts.md)
* Lär dig mer om [tjänstmeddelanden](monitoring-service-notifications.md)
* Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md)
