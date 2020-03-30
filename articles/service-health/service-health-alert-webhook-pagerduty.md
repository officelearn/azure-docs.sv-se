---
title: Skicka hälsoaviseringar för Azure-tjänsten med PagerDuty
description: Få anpassade aviseringar om tjänsthälsohändelser till din PagerDuty-instans.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: bb449a5279f3cea55e6aec2f72edfd11fb26227a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654077"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Skicka hälsoaviseringar för Azure-tjänsten med PagerDuty med webhooks

Den här artikeln visar hur du konfigurerar Azure-tjänsthälsomeddelanden via PagerDuty med hjälp av en webhook. Genom att använda [PagerDutys](https://www.pagerduty.com/)anpassade Microsoft Azure-integrationstyp kan du enkelt lägga till servicehälsoaviseringar till dina nya eller befintliga PagerDuty-tjänster.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Skapa url för integrering av tjänsthälsa i PagerDuty
1.  Kontrollera att du har registrerat dig för och är inloggad på ditt [PagerDuty-konto.](https://www.pagerduty.com/)

1.  Navigera till avsnittet **Tjänster** i PagerDuty.

    ![Avsnittet "Tjänster" i PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Välj **Lägg till ny tjänst** eller öppna en befintlig tjänst som du har konfigurerat.

1.  Välj följande i **integrationsinställningarna:**

    a. **Integrationstyp**: Microsoft Azure

    b. **Integration**namn \<: Namn\>

    !["Integrationsinställningarna" i PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Fyll i andra obligatoriska fält och välj **Lägg till**.

1.  Öppna den nya integreringen och kopiera och spara **integrations-URL:en**.

    !["Integration URL" i PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Skapa en avisering med PagerDuty i Azure-portalen
### <a name="for-a-new-action-group"></a>För en ny åtgärdsgrupp:
1. Följ steg 1 till och med 8 i [Skapa en avisering om en tjänsthälsan för en ny åtgärdsgrupp med hjälp av Azure-portalen](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Definiera i listan över **åtgärder:**

    a. **Åtgärdstyp:** *Webhook*

    b. **Detaljer:** **Url:en för PagerDuty-integrering** som du tidigare sparat.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **Spara** när du är klar för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig åtgärdsgrupp:
1. Välj **Övervaka**i [Azure-portalen](https://portal.azure.com/).

1. Välj **Åtgärdsgrupper** **Action groups**i avsnittet Inställningar .

1. Sök efter och markera den åtgärdsgrupp som du vill redigera.

1. Lägg till i listan över **åtgärder:**

    a. **Åtgärdstyp:** *Webhook*

    b. **Detaljer:** **Url:en för PagerDuty-integrering** som du tidigare sparat.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **Spara** när du vill uppdatera åtgärdsgruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran
1. Skapa servicehälsonyttolasten som du vill skicka. Du hittar en exempeltjänsthälsa webhook nyttolast på [Webhooks för Azure aktivitetsloggvarningar](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Du bör `202 Accepted` få ett med ett meddelande som innehåller ditt "händelse-ID".

1. Gå till [PagerDuty](https://www.pagerduty.com/) för att bekräfta att integreringen har konfigurerats.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problemhanteringssystem](service-health-alert-webhook-guide.md).
- Granska [webbkroksschemat för aktivitetsloggen.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- Läs mer om [hälsomeddelanden](../azure-monitor/platform/service-notifications.md)för tjänster .
- Läs mer om [åtgärdsgrupper](../azure-monitor/platform/action-groups.md).