---
title: "Konfigurera aviseringar för Azure-tjänstens hälsotillstånd med OpsGenie | Microsoft Docs"
description: "Hämta anpassade meddelanden om tjänstens hälsa händelser till OpsGenie-instans."
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: a2309a050225dd0d7ac8d5b3e4c762bc5bcb25c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Konfigurera tjänsten health-aviseringar med OpsGenie

Den här artikeln visar hur du ställer in hälsovarningar för Azure-tjänsten med OpsGenie med hjälp av en webhook. Med hjälp av [OpsGenie](https://www.opsgenie.com/)'s Azure Service hälsa Integration, du kan vidarebefordra hälsovarningar för Azure-tjänsten till OpsGenie. OpsGenie kan fastställa rätt personer att Avisera baserat på-samtal scheman med hjälp av e-post, textmeddelanden (SMS), telefonsamtal, iOS och Android push-meddelanden och växande aviseringar tills aviseringen bekräftats eller stängd.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Skapa en hälsa integration URL i OpsGenie
1.  Kontrollera att du har registrerat dig för och är inloggad på ditt [OpsGenie](https://www.opsgenie.com/) konto.

2.  Navigera till den **integreringar** avsnitt i OpsGenie.

    ![Avsnittet ”integreringar” i OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

3.  Välj den **Azure tjänstens hälsa** integrering knappen.

    ![”Azure tjänstens hälsa knappen” i OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

4.  **Namnet** din varning och ange den **tilldelats Team** fältet.

5.  Fyll i de andra fälten som **mottagare**, **aktiverad**, och **Ignorera meddelanden**.

6.  Kopiera och spara den **integrering URL**, som redan ska innehålla din `apiKey` läggas till i slutet.

    ![”Integration URL” i OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

7.  Välj **spara integrering**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Skapa en avisering med OpsGenie i Azure-portalen
### <a name="for-a-new-action-group"></a>För en ny grupp:
1. Följ steg 1 till 8 i [skapar en avisering på ett meddelande som tjänsten hälsa för en ny grupp med hjälp av Azure portal](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Definiera i listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhooken*

    b. **Information:** OpsGenie **integrering URL** du sparat tidigare.

    c. **Namn:** Webhook's, alias eller identifierare.

3. Välj **spara** när du är klar för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig grupp:
1. I den [Azure-portalen](https://portal.azure.com/)väljer **övervakaren**.

2. I den **inställningar** väljer **åtgärdsgrupper**.

3. Hitta och välj den grupp du vill redigera.

4. Lägga till listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhooken*

    b. **Information:** OpsGenie **integrering URL** du sparat tidigare.

    c. **Namn:** Webhook's, alias eller identifierare.

5. Välj **spara** när du är klar för att uppdatera åtgärdsgruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran
1. Skapa nyttolasten av tjänstens hälsa som du vill skicka. Du hittar en webhook exempel tjänstens hälsa nyttolast på [Webhooks för Azure aktiviteten Logga varningar](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Du bör få ett `200 OK` svar med meddelandet status ”lyckades”.

4. Gå till [OpsGenie](https://www.opsgenie.com/) att bekräfta att din integrering har ställts in.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurera webhook-meddelanden för befintliga problem system](service-health-alert-webhook-guide.md).
- Granska de [avisering webhook för aktivitetslogg](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Lär dig mer om [tjänsten meddelanden om hälsostatus](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Lär dig mer om [åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md).