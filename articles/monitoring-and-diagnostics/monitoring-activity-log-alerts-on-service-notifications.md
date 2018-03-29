---
title: Varningar aktivitet loggen på Azure tjänstmeddelanden | Microsoft Docs
description: Håll dig informerad via SMS, e-post eller webhook när Azure-tjänsten inträffar.
author: johnkemnetz
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: johnkem
ms.openlocfilehash: b4c4fdeb825bbcab54f074c5224140282a24d196
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
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

4. Ange ett namn i den **aktivitet avisering loggnamn** rutan och ange en **beskrivning**.

    ![Dialogrutan ”Lägg till loggen varning”](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group-sh.png)

5. Den **prenumeration** rutan autofills med din aktuella prenumeration. Den här prenumerationen används för att spara aktiviteten loggen aviseringen. Aviseringen resursen har distribuerats till den här prenumerationen och övervakar händelser i aktivitetsloggen för den.

6. Välj den **resursgruppen** i aviseringen resursen har skapats. Det är inte den resursgrupp som övervakas av aviseringen. Det är istället resursgruppen där aviseringen resursen finns.

7. Den **händelsekategori** automatiskt väljer **Hälsotjänst**. Alternativt, Välj den **Service**, **Region**, och **typen** av hälsa tjänstmeddelanden som du vill ta emot.

8. Under **avisering via**, Välj den **ny** åtgärdsknappen för gruppen. Ange ett namn i den **åtgärd gruppnamn** och anger ett namn i den **kortnamnet** rutan. Det korta namnet refereras i meddelanden som skickas när den här aviseringen utlöses.

9. Definiera en lista över mottagare genom att tillhandahålla mottagarens:

    a. **Namnet**: Ange mottagarens namn, alias eller identifierare.

    b. **Åtgärdstyp**: Välj SMS, e-post, webhook, Azure-program och mycket mer.

    c. **Information om**: baserat på typen av valt, ange ett telefonnummer, e-postadress, webhook URI och så vidare.

10. Välj **OK** att skapa aviseringen.

Inom några minuter aviseringen är aktiv och börjar att utlösa baserat på de villkor som du angav när du skapar.

Lär dig hur du [konfigurera webhook-meddelanden för befintliga problem system](../service-health/service-health-alert-webhook-guide.md). Information om webhook-schemat för aktiviteten loggen aviseringar finns [Webhooks för Azure aktiviteten Logga varningar](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Åtgärdsgruppen som definierats i de här stegen är återanvändbara som en befintlig grupp för alla framtida definitioner för aviseringen.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Skapa en avisering på ett meddelande med tjänstens hälsa för en befintlig grupp med hjälp av Azure portal

1. Följ steg 1 till 7 i föregående avsnitt för att skapa health service-meddelande. 

2. Under **avisering via**, Välj den **befintliga** åtgärdsknappen för gruppen. Markera gruppen lämplig åtgärd.

3. Välj **OK** att skapa aviseringen.

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
