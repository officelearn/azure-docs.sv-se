---
title: Skicka hälsovarningar för Azure-tjänsten med OpsGenie med webhooks
description: Få personliga aviseringar om hälsohändelser för tjänsten till din OpsGenie-instans.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: def12d5e7b1b93b8370cd7be61538fca53531ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654145"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Skicka hälsovarningar för Azure-tjänsten med OpsGenie med webhooks

Den här artikeln visar hur du konfigurerar Azure-tjänsthälsoaviseringar med OpsGenie med hjälp av en webhook. Genom att använda [OpsGenies](https://www.opsgenie.com/)Azure Service Health Integration kan du vidarebefordra Azure Service Health-aviseringar till OpsGenie. OpsGenie kan bestämma rätt personer att meddela baserat på jourscheman, med hjälp av e-post, textmeddelanden (SMS), telefonsamtal, iOS & Android push-meddelanden och eskalerande varningar tills aviseringen bekräftas eller stängs.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Skapa en URL för integrering av tjänsthälsa i OpsGenie
1.  Kontrollera att du har registrerat dig för och är inloggad på ditt [OpsGenie-konto.](https://www.opsgenie.com/)

1.  Navigera till avsnittet **Integrationer** i OpsGenie.

    ![Avsnittet "Integrationer" i OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Välj integrationsknappen **för Azure Service Health.**

    ![Knappen "Azure Service Health" i OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Namnge** aviseringen och ange fältet **Tilldelad till grupp.**

1.  Fyll i de andra fälten som **Mottagare,** **Aktiverad**och **Undertryck meddelanden**.

1.  Kopiera och spara **integrations-URL:en**, som redan ska innehålla den `apiKey` bifogade till slutet.

    !["Integration URL" i OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Välj **Spara integrering**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Skapa en avisering med OpsGenie i Azure-portalen
### <a name="for-a-new-action-group"></a>För en ny åtgärdsgrupp:
1. Följ steg 1 till och med 8 i [Skapa en avisering om en tjänsthälsan för en ny åtgärdsgrupp med hjälp av Azure-portalen](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Definiera i listan över **åtgärder:**

    a. **Åtgärdstyp:** *Webhook*

    b. **Detaljer:** **OpsGenie-integrations-URL:en** som du tidigare sparade.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **Spara** när du är klar för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig åtgärdsgrupp:
1. Välj **Övervaka**i [Azure-portalen](https://portal.azure.com/).

1. Välj **Åtgärdsgrupper** **Action groups**i avsnittet Inställningar .

1. Sök efter och markera den åtgärdsgrupp som du vill redigera.

1. Lägg till i listan över **åtgärder:**

    a. **Åtgärdstyp:** *Webhook*

    b. **Detaljer:** **OpsGenie-integrations-URL:en** som du tidigare sparade.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **Spara** när du vill uppdatera åtgärdsgruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran
1. Skapa servicehälsonyttolasten som du vill skicka. Du hittar en exempeltjänsthälsa webhook nyttolast på [Webhooks för Azure aktivitetsloggvarningar](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Du bör `200 OK` få ett svar med meddelandet om status "lyckades".

1. Gå till [OpsGenie](https://www.opsgenie.com/) för att bekräfta att din integration har konfigurerats.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problemhanteringssystem](service-health-alert-webhook-guide.md).
- Granska [webbkroksschemat för aktivitetsloggen.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- Läs mer om [hälsomeddelanden](../azure-monitor/platform/service-notifications.md)för tjänster .
- Läs mer om [åtgärdsgrupper](../azure-monitor/platform/action-groups.md).