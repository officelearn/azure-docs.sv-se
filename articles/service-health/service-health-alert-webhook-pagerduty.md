---
title: Konfigurera Azure service health-aviseringar med PagerDuty | Microsoft Docs
description: Få personligt anpassade meddelanden om service health-händelser till din PagerDuty-instans.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 11/14/2017
ms.openlocfilehash: b78c155fb2f3a13c27f4ff71c4dd37df2dbd2f36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621018"
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Konfigurera service health-aviseringar med PagerDuty

Den här artikeln visar hur du ställer in Azure service health meddelanden via PagerDuty med en webhook. Med hjälp av [PagerDuty](https://www.pagerduty.com/)'s anpassade typer för Microsoft Azure-integrering, du kan enkelt lägga till Service Health-aviseringar till dina nya eller befintliga PagerDuty-tjänster.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Skapa en service health integration URL i PagerDuty
1.  Kontrollera att du har registrerat dig för och är inloggad på ditt [PagerDuty](https://www.pagerduty.com/) konto.

1.  Navigera till den **Services** avsnittet i PagerDuty.

    ![Avsnittet ”tjänster” i PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Välj **Lägg till ny tjänst** eller öppna en befintlig tjänst som du har ställt in.

1.  I den **integrationsinställningar**, väljer du följande:

    a. **Integrationstyp**: Microsoft Azure

    b. **Namn på Integration**: \<Namn\>

    ![The "Integration Settings" in PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Fyll i alla obligatoriska fält och välj **Lägg till**.

1.  Öppna den här nya integreringen och kopiera och spara den **integrering URL**.

    ![”Integreringen URL” i PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Skapa en avisering med PagerDuty i Azure-portalen
### <a name="for-a-new-action-group"></a>För en ny åtgärdsgrupp:
1. Följ steg 1 till och med 8 i [skapa en avisering på en avisering om tjänstens hälsa för en ny åtgärdsgrupp med hjälp av Azure portal](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Definiera i listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhook*

    b. **Information:** PagerDuty **integrering URL** du sparat tidigare.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **spara** när du är klar för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig åtgärdsgrupp:
1. I den [Azure-portalen](https://portal.azure.com/)väljer **övervakaren**.

1. I den **inställningar** väljer **åtgärdsgrupper**.

1. Hitta och välj åtgärdsgrupp som du vill redigera.

1. Lägg till i listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhook*

    b. **Information:** PagerDuty **integrering URL** du sparat tidigare.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **spara** när du är klar för att uppdatera åtgärdsgruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa webhook-integrering via en HTTP POST-begäran
1. Skapa service health-nyttolasten som du vill skicka. Du hittar ett exempel service health webhook-nyttolasten på [Webhooks för Azure-aktivitetsloggar loggaviseringar](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran enligt följande:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Du bör få en `202 Accepted` med ett meddelande som innehåller din ”händelse-ID”

1. Gå till [PagerDuty](https://www.pagerduty.com/) att bekräfta att din integrering har har ställts in.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurera webhook-aviseringar för befintliga problem system](service-health-alert-webhook-guide.md).
- Granska den [avisering webhook för aktivitetslogg](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Lär dig mer om [service health meddelanden](../azure-monitor/platform/service-notifications.md).
- Läs mer om [åtgärdsgrupper](../azure-monitor/platform/action-groups.md).