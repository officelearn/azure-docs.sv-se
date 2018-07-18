---
title: Översikt över klassiska aviseringar i Microsoft Azure och Azure Monitor
description: Aviseringar kan du övervaka Azure Resursmått, evenemang eller loggar och meddelas när ett angivet villkor uppfylls.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114019"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Vad är klassiska aviseringar i Microsoft Azure?

> [!NOTE]
> Den här artikeln beskriver hur du skapar äldre, klassiska måttaviseringar. Azure Monitor har nu stöd [måttaviseringar för nyare, nära realtid tid och nya aviseringsgränssnittet](monitoring-overview-unified-alerts.md). 
>

Med hjälp av aviseringar, du kan konfigurera villkor för över data och bli informerad när villkoren matchas senaste övervakningsdata.


## <a name="alerts-on-azure-monitor-data"></a>Aviseringar i Azure Monitor-data
Det finns två typer av klassiska aviseringar: måttaviseringar och aktivitetsloggaviseringar.

* **Klassiska måttaviseringar**: den här aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Aviseringen genererar en avisering när it är ”aktiverad” (när tröskelvärdet skärs och aviseringstillståndet uppfylls). Det skapar också en avisering när det är ”löst” (när tröskelvärdet skärs igen och villkoret uppfylls inte längre). 

* **Klassiska aktivitetsloggaviseringar**: den här strömmande log-avisering utlöses när en händelse i aktivitetsloggen genereras som matchar filtervillkor som du har tilldelat. Dessa aviseringar har endast en tillstånd, ”aktivera”, eftersom aviseringen motorn gäller bara filtervillkoren som en ny händelse. Dessa aviseringar kan meddela dig när en ny Service Health-incident inträffar eller när en användare eller program utför en åtgärd i din prenumeration som ”ta bort virtuell dator”.

Dirigera data till Log Analytics (tidigare Operations Management Suite) för att ta emot loggar med diagnostikdata tillgängliga som är tillgänglig via Azure Monitor, och Använd en avisering i Log Analytics-fråga. Logga Analytics nu använder den [nya aviseringar metoden](monitoring-overview-unified-alerts.md). 

Följande diagram sammanfattar datakällor i Azure Monitor och ger förslag på hur du kan meddela av dessa data.

![Aviseringar som förklaras](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taxonomi Azure Monitor-aviseringar (klassisk)
Azure använder följande termer för att beskriva klassiska aviseringar och deras funktioner:
* **Varning**: definitionen av villkoren (en eller flera regler eller villkor) som blir aktiveras när uppfylls.
* **Aktiva**: tillståndet som uppstår när de kriterier som definieras av en klassisk aviseringen är uppfyllt.
* **Löst**: tillståndet som uppstår när de kriterier som definieras av en klassisk avisering är inte längre uppfyllt när du tidigare har uppfyllts.
* **Meddelande**: vad som händer baserat på när en avisering om klassiska blir aktiv.
* **Åtgärden**: anropet som skickas till mottagaren av ett meddelande (till exempel ett e-postmeddelande eller ett inlägg till en webhook-URL). Meddelanden kan vanligtvis utlösa flera åtgärder.

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>Hur jag för att ta emot meddelanden från en klassisk Azure Monitor-avisering?
Historiskt sett används Azure-aviseringar från olika tjänster för sina egna inbyggda aviserings-metoder. 

Nu Azure Monitor erbjuder återanvändbara meddelande gruppering kallas *åtgärdsgrupper*. Åtgärdsgrupper ange en uppsättning mottagare för ett meddelande. När en avisering aktiveras som refererar till åtgärdsgruppen, får alla mottagare som meddelanden. Den här funktionen kan du återanvända en gruppering av mottagare (till exempel på anrop engineer listan) för många aviseringar objekt. Åtgärdsgrupper använda meddelanden via olika metoder. Dessa metoder kan innehålla publicera till en webhook-URL, skicka e-post, SMS-meddelanden och ett antal andra åtgärder. Mer information finns i [skapa och hantera åtgärdsgrupper i Azure-portalen](monitoring-action-groups.md). 

Äldre klassiska aktivitetsloggaviseringar använda åtgärdsgrupper.

Äldre måttaviseringar kan dock inte använda åtgärdsgrupper. I stället kan du konfigurera följande åtgärder: 
* Skicka e-postmeddelanden till tjänstadministratör delade administratörer, eller till ytterligare e-postadresser som du anger.
* Anropa en webhook, där du kan starta ytterligare automation-åtgärder.

Webhooks aktivera automatisering och åtgärder, till exempel med hjälp av följande tjänster:
- Azure Automation – Runbook
- Azure Functions
- Azure Logic App
- En tjänst från tredje part

## <a name="next-steps"></a>Nästa steg
Få information om Varningsregler och hur du konfigurerar dem med hjälp av följande dokumentation:

* Läs mer om [mått](monitoring-overview-metrics.md)
* Konfigurera [klassiska måttaviseringar med hjälp av Azure portal](insights-alerts-portal.md)
* Konfigurera [klassiska måttaviseringar med hjälp av PowerShell](insights-alerts-powershell.md)
* Konfigurera [klassiska måttaviseringar med hjälp av Azure CLI](insights-alerts-command-line-interface.md)
* Konfigurera [klassiska måttaviseringar med hjälp av REST-API i Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Läs mer om [aktivitetsloggar](monitoring-overview-activity-logs.md)
* Konfigurera [aktivitetsloggaviseringar med hjälp av Azure portal](monitoring-activity-log-alerts.md)
* Konfigurera [aktivitetsloggaviseringar med hjälp av Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Granska den [avisering webhook för aktivitetslogg](monitoring-activity-log-alerts-webhook.md)
* Läs mer om [åtgärdsgrupper](monitoring-action-groups.md)
* Konfigurera [nyare aviseringar](monitor-alerts-unified-usage.md)
