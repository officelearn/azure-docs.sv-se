---
title: Varningar aktivitet loggen på Azure-tjänstens meddelanden
description: Håll dig informerad via SMS, e-post eller webhook när Azure-tjänsten inträffar.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 01dc3a3c6489b694af26c78ae3b4756f3e8f00b7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263124"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Skapa aktivitet loggen aviseringar på tjänstmeddelanden
## <a name="overview"></a>Översikt
Den här artikeln visar hur du ställer in aktiviteten loggen aviseringar för meddelanden om hälsostatus med hjälp av Azure portal.  

Du kan ta emot en avisering när Azure skickar meddelanden om hälsostatus till din Azure-prenumeration. Du kan konfigurera aviseringen baserat på:

- Klassen för tjänsten hälsa meddelande (tjänsten problem, planerat underhåll hälsa rekommendationerna).
- Den prenumeration som påverkas.
- Tjänster som påverkas.
- Regionerna påverkas.

Du kan också konfigurera som aviseringen ska skickas till:

- Välj en befintlig grupp.
- Skapa en ny grupp (som kan användas för framtida aviseringar).

Läs mer om åtgärdsgrupper i [skapa och hantera åtgärdsgrupper](monitoring-action-groups.md).

Information om hur du konfigurerar tjänsten hälsovarningar för meddelande med hjälp av Azure Resource Manager-mallar finns i [Resource Manager-mallar](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Skapa en avisering på ett meddelande som tjänsten hälsa för en ny grupp med hjälp av Azure portal
1. I den [portal](https://portal.azure.com)väljer **Hälsotjänst**.

    ![Tjänsten ”Hälsotjänst”](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. I den **aviseringar** väljer **hälsovarningar**.

    ![Fliken ”hälsovarningar”](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. Välj **skapa tjänsten hälsoavisering** och Fyll i fälten.

    ![Kommandot ”hälsoavisering för skapa”](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. Välj den **prenumeration**, **Services**, och **regioner** du vill bli aviserad för.

    ![Dialogrutan ”Lägg till loggen varning”](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Den här prenumerationen används för att spara aktiviteten loggen aviseringen. Aviseringen resursen har distribuerats till den här prenumerationen och övervakar händelser i aktivitetsloggen för den.

5. Välj den **händelsetyper** du vill bli aviserad för: *tjänsten problemet*, *planerat underhåll*, och *hälsa rekommendationerna* 

6. Ange din information genom att ange en **varningsregelns namn** och **beskrivning**.

7. Välj den **resursgruppen** där du vill att aviseringen ska sparas.

8. Skapa en ny grupp genom att välja **ny grupp**. Ange ett namn i den **åtgärd gruppnamn** och anger ett namn i den **kortnamnet** rutan. Det korta namnet refereras i meddelanden som skickas när den här aviseringen utlöses.

    ![Skapa en ny grupp](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

9. Definiera en lista över mottagare genom att tillhandahålla mottagarens:

    a. **Namnet**: Ange mottagarens namn, alias eller identifierare.

    b. **Åtgärdstyp**: Välj SMS, e-post, webhook, Azure-program och mycket mer.

    c. **Information om**: baserat på typen av valt, ange ett telefonnummer, e-postadress, webhook URI och så vidare.

10. Välj **OK** att skapa åtgärdsgruppen och sedan **skapa varningsregeln** att slutföra aviseringen.

Inom några minuter aviseringen är aktiv och börjar att utlösa baserat på de villkor som du angav när du skapar.

Lär dig hur du [konfigurera webhook-meddelanden för befintliga problem system](../service-health/service-health-alert-webhook-guide.md). Information om webhook-schemat för aktiviteten loggen aviseringar finns [Webhooks för Azure aktiviteten Logga varningar](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Åtgärdsgruppen som definierats i de här stegen är återanvändbara som en befintlig grupp för alla framtida definitioner för aviseringen.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Skapa en avisering på ett meddelande med tjänstens hälsa för en befintlig grupp med hjälp av Azure portal

1. Följ steg 1 till 7 i föregående avsnitt för att skapa health service-meddelande. 

2. Under **definiera grupp**, klicka på den **väljer grupp** knappen. Markera gruppen lämplig åtgärd.

3. Välj **Lägg till** att lägga till åtgärdsgruppen och sedan **skapa varningsregeln** att slutföra aviseringen.

Inom några minuter aviseringen är aktiv och börjar att utlösa baserat på de villkor som du angav när du skapar.

## <a name="manage-your-alerts"></a>Hantera aviseringar

När du skapar en avisering, är det visas i den **aviseringar** avsnitt i **övervakaren**. Väljer du den avisering du vill hantera att:

* Redigera den.
* Ta bort den.
* Inaktivera eller aktivera den, om du vill att tillfälligt stoppa eller återuppta tar emot meddelanden om aviseringen.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurera webhook-meddelanden för befintliga problem system](../service-health/service-health-alert-webhook-guide.md).
- Lär dig mer om [tjänsten meddelanden om hälsostatus](monitoring-service-notifications.md).
- Lär dig mer om [meddelande hastighetsbegränsning](monitoring-alerts-rate-limiting.md).
- Granska de [avisering webhook för aktivitetslogg](monitoring-activity-log-alerts-webhook.md).
- Hämta en [översikt över aktivitet loggen aviseringar](monitoring-overview-alerts.md), och lära dig hur du tar emot aviseringar. 
- Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md).
