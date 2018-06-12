---
title: Skapa och hantera åtgärdsgrupper i Azure-portalen
description: Lär dig hur du skapar och hanterar åtgärdsgrupper i Azure-portalen.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/1/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 63216d56fb3acbb954086fbf026441e69073621e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263073"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Skapa och hantera åtgärdsgrupper i Azure-portalen
## <a name="overview"></a>Översikt ##
En grupp är en samling felaviseringar som definierats av användaren. Azure-Monitor och tjänstens hälsa för aviseringar är konfigurerade för att använda en specifik åtgärdsgrupp när en avisering utlöses. Olika typer av aviseringar kan använda samma grupp eller grupper av olika åtgärder beroende på användarens krav.

Den här artikeln visar hur du skapar och hanterar åtgärdsgrupper i Azure-portalen.

Varje åtgärd består av följande egenskaper:

* **Namnet**: en unik identifierare inom åtgärdsgruppen.  
* **Åtgärdstyp**: skicka ett vanligt telefonsamtal eller SMS, skicka ett e-postmeddelande, anropa en webhook, skicka data till ett ITSM verktyg, anropa en Logikapp, skickar push-meddelanden till appen Azure eller köra en Automation-runbook.
* **Information om**: motsvarande phone nummer, e-postadress, webhook URI eller ITSM anslutningsinformation.

Information om hur du använder Azure Resource Manager-mallar för att konfigurera åtgärdsgrupper finns [åtgärd grupp Resource Manager-mallar](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Skapa en grupp med hjälp av Azure portal ##
1. I den [portal](https://portal.azure.com)väljer **övervakaren**. Den **övervakaren** bladet konsoliderar alla dina övervakning inställningar och data i en vy.

    ![Tjänsten ”Övervakaren”](./media/monitoring-action-groups/home-monitor.png)
2. I den **inställningar** väljer **åtgärdsgrupper**.

    ![Fliken ”åtgärdsgrupper”](./media/monitoring-action-groups/action-groups-blade.png)
3. Välj **Lägg till grupp**, och Fyll i fälten.

    ![Kommandot ”Lägg till grupp”](./media/monitoring-action-groups/add-action-group.png)
4. Ange ett namn i den **åtgärd gruppnamn** och ange ett namn i den **kortnamnet** rutan. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

      ![Dialogrutan Lägg till grupp för åtgärden ”](./media/monitoring-action-groups/action-group-define.png)

5. Den **prenumeration** rutan autofills med din aktuella prenumeration. Den här prenumerationen är den som åtgärdsgruppen sparas.

6. Välj den **resursgruppen** i åtgärdsgruppen har sparats.

7. Definiera en lista med åtgärder genom att ge varje åtgärd:

    a. **Namnet**: Ange en unik identifierare för den här åtgärden.

    b. **Åtgärdstyp**: Välj e-post/SMS/Push/röst Logikapp, Webhook, ITSM eller Automation-Runbook.

    c. **Information om**: baserat på typen av Ange telefonnummer, e-postadress, webhook URI, Azure-app, ITSM anslutning eller Automation-runbook. Ange dessutom för ITSM åtgärd **arbetsobjekt** och andra fält ITSM-verktyget kräver.

8. Välj **OK** att skapa åtgärdsgruppen.

## <a name="action-specific-information"></a>Information om specifika
<dl>
<dt>Azure-app Push</dt>
<dd>Du kan ha upp till 10 Azure-program-åtgärder i en grupp.</dd>
<dd>Åtgärden Azure-program stöder endast ServiceHealth aviseringar just nu. Andra avisering tid kommer att ignoreras. Se [konfigurera aviseringar när ett meddelande om tjänstens hälsa är bokförd](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>E-post</dt>
<dd>E-postmeddelanden skickas från följande e-postadresser. Se till att din e-filtrering har konfigurerats korrekt

    - azure-noreply@microsoft.com
    - azureemail-noreply@microsoft.com
    - alerts-noreply@mail.windowsazure.com
    
</dd>
<dd>Du kan ha upp till 1 000 e-post-åtgärder i en grupp</dd>
<dd>Finns det [hastighet begränsa information](./monitoring-alerts-rate-limiting.md) artikel</dd>

<dt>ITSM</dt>
<dd>Du kan ha upp till 10 ITSM åtgärder i en grupp</dd>
<dd>ITSM-åtgärden kräver en ITSM-anslutning. Lär dig hur du skapar en [ITSM anslutning](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>Logikapp</dt>
<dd>Du kan ha upp till 10 Logikapp åtgärder i en grupp</dd>

<dt>runbook</dt>
<dd>Du kan ha upp till 10 Runbook-åtgärder i en grupp</dd>

<dt>SMS</dt>
<dd>Du kan ha upp till 10 SMS-åtgärder i en grupp</dd>
<dd>Finns det [hastighet begränsa information](./monitoring-alerts-rate-limiting.md) artikel</dd>
<dd>Finns det [SMS Varna beteende](monitoring-sms-alert-behavior.md) artikel</dd>

<dt>röst</dt>
<dd>Du kan ha upp till 10 röst åtgärder i en grupp</dd>
<dd>Finns det [hastighet begränsa information](./monitoring-alerts-rate-limiting.md) artikel</dd>

<dt>Webhook</dt>
<dd>Du kan ha upp till 10 Webhook-åtgärder i en grupp
<dd>Försök logik - tidsgränsen för svar är 10 sekunder. Webhook-anropet kommer att vara högst 2 tider när följande HTTP-Statuskoder returneras på nytt: 408 429, 503, 504 eller HTTP-slutpunkten inte svarar. Det första förnyade sker efter 10 sekunder. Andra och senaste försök sker efter 100 sekunder.</dd>
</dl>

## <a name="manage-your-action-groups"></a>Hantera åtgärdsgrupper ##
När du skapar en grupp, är det visas i den **åtgärdsgrupper** avsnitt i den **övervakaren** bladet. Välj den grupp du vill hantera att:

* Lägga till, redigera eller ta bort åtgärder.
* Ta bort åtgärdsgruppen.

## <a name="next-steps"></a>Nästa steg ##
* Lär dig mer om [SMS Varna beteende](monitoring-sms-alert-behavior.md).  
* Få en [förståelse av aviseringen webhook för aktivitetslogg](monitoring-activity-log-alerts-webhook.md).  
* Lär dig mer om [ITSM koppling](../log-analytics/log-analytics-itsmc-overview.md)
* Lär dig mer om [hastighetsbegränsning](monitoring-alerts-rate-limiting.md) aviseringar för.
* Hämta en [översikt över aktivitet loggen aviseringar](monitoring-overview-alerts.md), och lära dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurera aviseringar när ett meddelande om tjänstens hälsa är bokförd](monitoring-activity-log-alerts-on-service-notifications.md).
