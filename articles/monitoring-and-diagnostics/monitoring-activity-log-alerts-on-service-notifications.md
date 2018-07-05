---
title: Ta emot aviseringar för aktivitetsloggar för Azure tjänstmeddelanden
description: Bli informerad via SMS, e-post eller webhook när Azure-tjänsten inträffar.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 1e79fee75b2361dd7da8c46c175a5a6532089ad6
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437247"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Skapa aviseringar för aktivitetsloggen för tjänstmeddelanden
## <a name="overview"></a>Översikt
Den här artikeln visar hur du ställer in varningar för aktivitetsloggar för meddelanden om hälsostatus för tjänsten med hjälp av Azure portal.  

Du kan få en avisering när Azure skickar meddelanden om hälsostatus för tjänsten på Azure-prenumerationen. Du kan konfigurera aviseringen baserat på:

- Klassen i aviseringen om tjänstens hälsa (tjänstproblem, planerat underhåll hälsoråd).
- Den prenumeration som påverkas.
- Tjänster som påverkas.
- Region(er) som påverkas.

> [!NOTE]
> Meddelanden om hälsostatus för tjänsten skickar inte en avisering om resource health-händelser.

Du kan också konfigurera som aviseringen ska skickas till:

- Välj en befintlig åtgärdsgrupp.
- Skapa en ny åtgärdsgrupp (som kan användas för framtida aviseringar).

Läs mer om åtgärdsgrupper i [Skapa och hantera åtgärdsgrupper](monitoring-action-groups.md).

Information om hur du konfigurerar tjänsten hälsovarningar för meddelande med hjälp av Azure Resource Manager-mallar finns i [Resource Manager-mallar](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Skapa en avisering på en avisering om tjänstens hälsa för en ny åtgärdsgrupp med hjälp av Azure portal
1. I den [portal](https://portal.azure.com)väljer **Tjänstehälsa**.

    ![Tjänsten ”Tjänstehälsa”](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. I den **aviseringar** väljer **hälsovarningar**.

    ![Fliken ”hälsovarningar”](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. Välj **skapa hälsoavisering för tjänst** och Fyll i fälten.

    ![Kommandot ”Skapa avisering för tjänsthälsa”](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. Välj den **prenumeration**, **Services**, och **regioner** du vill få aviseringar.

    ![Dialogrutan ”Lägg till aktivitetsloggavisering”](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Den här prenumerationen används för att spara aktivitetsloggaviseringen. Aviseringen resursen har distribuerats till den här prenumerationen och övervakar händelser i aktivitetsloggen för den.

5. Välj den **händelsetyper** du vill få aviseringar: *tjänsten problemet*, *planerat underhåll*, och *hälsorådgivning* 

6. Definiera din Aviseringsinformationen genom att ange en **varningsregelns namn** och **beskrivning**.

7. Välj den **resursgrupp** där du vill att aviseringen ska sparas.

8. Skapa en ny åtgärdsgrupp genom att välja **ny åtgärdsgrupp**. Ange ett namn i den **namn på åtgärdsgrupp** och anger ett namn i den **kortnamnet** box. Det korta namnet refereras i meddelanden som skickas när den här varningen utlöses.

    ![Skapa en ny åtgärdsgrupp](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

9. Definiera en lista över mottagare genom att tillhandahålla mottagarens:

    a. **Namn på**: Ange mottagarens namn, alias eller identifierare.

    b. **Åtgärdstyp**: Välj SMS, e-post, webhook, Azure-app med mera.

    c. **Information om**: baserat på typen av valt, ange ett telefonnummer, e-postadress, webhook URI osv.

10. Välj **OK** att skapa åtgärdsgruppen och sedan **skapa varningsregel** att slutföra aviseringen.

Inom några minuter, aviseringen är aktiv och börjar att utlösa baserat på de villkor som du angav när du skapar.

Lär dig hur du [konfigurera webhook-aviseringar för befintliga problem system](../service-health/service-health-alert-webhook-guide.md). Information om webhook-schema för aktivitetsloggaviseringar finns [Webhooks för Azure-aktivitetsloggar loggaviseringar](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Åtgärdsgrupp som definierats i de här stegen är återanvändbara som en befintlig åtgärdsgrupp för alla framtida varningsdefinitioner.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Skapa en avisering på en avisering om tjänstens hälsa för en befintlig åtgärdsgrupp med hjälp av Azure portal

1. Följ steg 1 till och med 7 i föregående avsnitt för att skapa aviseringen om tjänstens hälsa. 

2. Under **definiera åtgärdsgruppen**, klickar du på den **väljer åtgärdsgrupp** knappen. Välj lämplig åtgärdsgruppen.

3. Välj **Lägg till** att lägga till åtgärdsgrupp och sedan **skapa varningsregel** att slutföra aviseringen.

Inom några minuter, aviseringen är aktiv och börjar att utlösa baserat på de villkor som du angav när du skapar.

## <a name="manage-your-alerts"></a>Hantera aviseringar

När du skapar en avisering, är det visas i den **aviseringar** delen av **övervakaren**. Välj den avisering du vill hantera till:

* Redigera den.
* Ta bort den.
* Inaktivera eller aktivera den, om du vill att tillfälligt stoppa eller återuppta tar emot meddelanden om aviseringen.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurera webhook-aviseringar för befintliga problem system](../service-health/service-health-alert-webhook-guide.md).
- Lär dig mer om [service health meddelanden](monitoring-service-notifications.md).
- Lär dig mer om [meddelande hastighetsbegränsning](monitoring-alerts-rate-limiting.md).
- Granska den [avisering webhook för aktivitetslogg](monitoring-activity-log-alerts-webhook.md).
- Hämta en [översikt över aktivitetsloggaviseringar](monitoring-overview-alerts.md), och lär dig hur du får aviseringar. 
- Läs mer om [åtgärdsgrupper](monitoring-action-groups.md).
