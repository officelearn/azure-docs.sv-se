---
title: Konfigurera aviseringar för Azure-tjänstens hälsotillstånd med PagerDuty | Microsoft Docs
description: Hämta anpassade meddelanden om tjänstens hälsa händelser till PagerDuty-instans.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 6e9fcf20d368e270f9af4551c539acd873335498
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Konfigurera tjänsten health-aviseringar med PagerDuty

Den här artikeln visar hur du ställer in meddelanden om hälsostatus för Azure-tjänsten via PagerDuty med hjälp av en webhook. Med hjälp av [PagerDuty](https://www.pagerduty.com/)'s anpassad typ för Microsoft Azure-integrering, du kan lägga till tjänstens hälsa aviseringar utan problem till ny eller befintlig PagerDuty tjänster.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Skapa en hälsa integration URL i PagerDuty
1.  Kontrollera att du har registrerat dig för och är inloggad på ditt [PagerDuty](https://www.pagerduty.com/) konto.

2.  Navigera till den **Services** avsnitt i PagerDuty.

    ![Avsnittet ”tjänster” i PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

3.  Välj **Lägg till ny tjänst** eller öppna en befintlig tjänst som du har ställt in.

4.  I den **inställningar för katalogintegrering**, Välj följande:

    a. **Integrering av typen**: Microsoft Azure

    b. **Integrering med namnet**: \<namn\>

    ![”Integration Settings” i PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

5.  Fyll i obligatoriska fält och markera **Lägg till**.

6.  Öppna ny integrationen och kopiera och spara den **integrering URL**.

    ![”Integration URL” i PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Skapa en avisering med PagerDuty i Azure-portalen
### <a name="for-a-new-action-group"></a>För en ny grupp:
1. Följ steg 1 till 8 i [skapar en avisering på ett meddelande som tjänsten hälsa för en ny grupp med hjälp av Azure portal](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Definiera i listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhooken*

    b. **Information:** PagerDuty **integrering URL** du sparat tidigare.

    c. **Namn:** Webhook's, alias eller identifierare.

3. Välj **spara** när du är klar för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig grupp:
1. I den [Azure-portalen](https://portal.azure.com/)väljer **övervakaren**.

2. I den **inställningar** väljer **åtgärdsgrupper**.

3. Hitta och välj den grupp du vill redigera.

4. Lägga till listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhooken*

    b. **Information:** PagerDuty **integrering URL** du sparat tidigare.

    c. **Namn:** Webhook's, alias eller identifierare.

5. Välj **spara** när du är klar för att uppdatera åtgärdsgruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran
1. Skapa service hälsa nyttolast som du vill skicka. Du hittar en exempel-tjänsten hälsa webhook nyttolast på [Webhooks för Azure aktiviteten Logga varningar](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Du bör få en `202 Accepted` med ett meddelande som innehåller ditt ”händelse-ID”

4. Gå till [PagerDuty](https://www.pagerduty.com/) att bekräfta att din integrering har ställts in.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurera webhook-meddelanden för befintliga problem system](service-health-alert-webhook-guide.md).
- Granska de [avisering webhook för aktivitetslogg](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Lär dig mer om [tjänsten meddelanden om hälsostatus](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Lär dig mer om [åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md).