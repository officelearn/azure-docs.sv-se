---
title: Skapa aktivitet loggen aviseringar | Microsoft Docs
description: "Ett meddelande via SMS, webhook och e-post när vissa händelser inträffar i aktivitetsloggen."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: johnkem
ms.openlocfilehash: 3885469ec0e1fcc31386dd0ad7fe6cb5d03ab28e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="create-activity-log-alerts"></a>Skapa aktivitet Logga varningar

## <a name="overview"></a>Översikt
Aktiviteten loggen aviseringar är aviseringar som aktiverar när en ny aktivitet logga en händelse inträffar som matchar de villkor som anges i aviseringen. De är Azure-resurser, så att de kan skapas med hjälp av en Azure Resource Manager-mall. De kan också skapa, uppdatera, eller tas bort i Azure-portalen. Den här artikeln beskriver begrepp bakom aktivitet loggen aviseringar. Den sedan visar hur du använder Azure-portalen för att ställa in en avisering på aktiviteten logghändelser.

Normalt skapar du aktiviteten loggen aviseringar för att ta emot meddelanden när:

* Vissa ändringar sker för resurser i din Azure-prenumeration som omfattar ofta enskilda resursgrupper eller resurser. Du kanske vill meddelas när en virtuell dator i myProductionResourceGroup tas bort. Eller så kanske du vill meddelas om några nya roller har tilldelats en användare i din prenumeration.
* En service hälsa händelse inträffar. Tjänsten hälsa händelser innehåller meddelanden om incidenter och underhållshändelser som gäller för resurser i din prenumeration.

I båda fallen övervakar en aktivitet loggen avisering bara för händelser i prenumerationen som aviseringen skapades.

Du kan konfigurera en aktivitet loggen avisering baserat på någon översta egenskap i JSON-objekt för en aktivitet händelselogg. Dock visar portalen de vanligaste alternativen:

- **Kategori**: administrativa, tjänsten hälsa och Autoskala rekommendation. Mer information finns i [översikt över Azure aktivitetsloggen](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Mer information om hälsa av tjänsten-händelser finns [varningar aktivitet loggen på tjänstmeddelanden](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Resursgrupp**
- **Resurs**
- **Resurstyp**
- **Åtgärdsnamnet**: The Resource Manager Role-Based åtkomstkontroll åtgärdsnamn.
- **Nivå**: allvarlighetsgrad för händelse (utförlig information, varning, fel eller kritiskt).
- **Status för**: status för händelsen, startas normalt, misslyckades eller lyckades.
- **Händelse som initieras av**: kallas även ”anroparen”. E-postadressen eller Azure Active Directory-identifieraren för användaren som utförde åtgärden.

>[!NOTE]
>Du måste ange minst två av de föregående villkoren i aviseringen, med en är kategorin. Du kan inte skapa en avisering som aktiveras varje gång en händelse skapas i aktivitetsloggarna.
>
>

När en aktivitet loggen avisering aktiveras använder en grupp för att generera meddelanden eller åtgärder. En grupp är en återanvändbar uppsättning notification mottagare, till exempel e-postadresser, webhook URL: er eller SMS telefonnummer. Mottagarna kan refereras från flera aviseringar centralisera och gruppera dina aviseringskanaler. När du definierar aviseringen aktivitet loggen har du två alternativ. Du kan:

* Använd en befintlig grupp i din logg varning. 
* Skapa en ny grupp. 

Läs mer om åtgärdsgrupper i [skapa och hantera åtgärdsgrupper i Azure portal](monitoring-action-groups.md).

Läs mer om meddelanden om hälsostatus i [varningar aktivitet loggen på meddelanden om hälsostatus](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Skapa en avisering på en aktivitet logga en händelse med en ny grupp med hjälp av Azure portal
1. I den [portal](https://portal.azure.com)väljer **övervakaren**.

    ![Tjänsten ”Övervakaren”](./media/monitoring-activity-log-alerts/home-monitor.png)
2. I den **aktivitetsloggen** väljer **aviseringar**.

    ![Fliken ”aviseringar”](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. Välj **Lägg till aktivitet loggen avisering**, och Fyll i fälten.

4. Ange ett namn i den **aktivitet avisering loggnamn** och välj en **beskrivning**.

    ![Kommandot ”Lägg till aktivitet loggen avisering”](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. Den **prenumeration** rutan autofills med din aktuella prenumeration. Den här prenumerationen är den som åtgärdsgruppen sparas. Aviseringen resursen har distribuerats till den här prenumerationen och övervakar aktivitetshändelser från den.

    ![Dialogrutan ”Lägg till loggen varning”](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. Välj den **resursgruppen** i aviseringen resursen har skapats. Detta är inte den resursgrupp som övervakas av aviseringen. Det är istället resursgruppen där aviseringen resursen finns.

7. Du kan också välja en **händelsekategori** att ändra ytterligare filter som visas. För administrativa händelser filtren är **resursgruppen**, **resurs**, **resurstypen**, **åtgärdsnamn**, **Nivå**, **Status**, och **händelse som initieras av**. Dessa värden identifiera vilka händelser som bör du övervaka den här aviseringen.

    >[!NOTE]
    >Du måste ange minst en av de föregående villkoren i aviseringen. Du kan inte skapa en avisering som aktiveras varje gång en händelse skapas i aktivitetsloggarna.
    >
    >

8. Ange ett namn i den **åtgärd gruppnamn** och ange ett namn i den **kortnamnet** rutan. Det korta namnet används i stället för en fullständig åtgärd gruppnamn när meddelanden som skickas med den här gruppen.

9.  Definiera en lista med åtgärder genom att tillhandahålla åtgärden:

    a. **Namnet**: Ange åtgärdens namn, alias eller identifierare.

    b. **Åtgärdstyp**: Välj SMS, e-post eller webhooken.

    c. **Information om**: baserat på typen av, ange ett telefonnummer, e-postadress eller webhook URI.

10. Välj **OK** att skapa aviseringen.

Aviseringen tar några minuter att sprida fullständigt och sedan aktiveras. Utlöser nya händelser som matchar den avisering kriterier.

Mer information finns i [förstå webhook-schemat som används i loggen Aktivitetsaviseringar](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Åtgärdsgruppen som definierats i de här stegen är återanvändbara som en befintlig grupp för alla framtida definitioner för aviseringen.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Skapa en avisering på en aktivitet logga en händelse för en befintlig grupp med hjälp av Azure portal
1. Följ steg 1 till 7 i föregående avsnitt för att skapa aviseringen aktivitet loggen.

2. Under **meddela**, Välj den **befintliga** åtgärdsknappen för gruppen. Välj en befintlig grupp i listan.

3. Välj **OK** att skapa aviseringen.

Aviseringen tar några minuter att sprida fullständigt och sedan aktiveras. Utlöser nya händelser som matchar den avisering kriterier.

## <a name="manage-your-alerts"></a>Hantera aviseringar

När du skapar en avisering, är det visas i avsnittet aviseringar i övervakaren bladet. Väljer du den avisering du vill hantera att:

* Redigera den.
* Ta bort den.
* Inaktivera eller aktivera den, om du vill att tillfälligt stoppa eller återuppta tar emot meddelanden om aviseringen.

## <a name="next-steps"></a>Nästa steg
- Hämta en [översikt över aviseringar](monitoring-overview-alerts.md).
- Lär dig mer om [meddelande hastighetsbegränsning](monitoring-alerts-rate-limiting.md).
- Granska de [avisering webhook för aktivitetslogg](monitoring-activity-log-alerts-webhook.md).
- Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md).  
- Lär dig mer om [tjänsten meddelanden om hälsostatus](monitoring-service-notifications.md).
- Skapa en [loggen varning att övervaka alla Autoskala motorn åtgärder för din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Skapa en [loggen varning att övervaka alla misslyckade Autoskala skala-/ skalbar åtgärder för din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
