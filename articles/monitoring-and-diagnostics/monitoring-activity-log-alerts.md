---
title: Skapa aviseringar för klassiska aktivitetsloggen
description: Meddelas via SMS, webhook och e-post när vissa händelser i aktivitetsloggen.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/18/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 120fd3552ad36b3d19179f39ca95ce2b3ee2c2e6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426626"
---
# <a name="create-activity-log-alerts-classic"></a>Skapa aviseringar (klassisk) för aktivitetsloggen

## <a name="overview"></a>Översikt
Aktivitetsloggsaviseringar är aviseringar som aktiverar när en ny händelse i aktivitetsloggen inträffar som matchar de villkor som anges i aviseringen. De är Azure-resurser, så att de kan skapas med hjälp av en Azure Resource Manager-mall. De kan också vara skapas, uppdateras eller tas bort i Azure-portalen. Den här artikeln beskrivs koncepten bakom aktivitetsloggaviseringar. Den sedan visar hur du använder Azure-portalen för att ställa in en avisering på händelser i aktivitetsloggen.

> [!NOTE]

>  Den nya [aviseringar](monitoring-overview-unified-alerts.md) upplevelse har ersatt den här proceduren. Den här artikeln ges som referens för den tidigare upplevelsen. [Läs mer](monitoring-activity-log-alerts-new-experience.md).

Normalt kan du skapa aviseringar för aktivitetsloggen kan ta emot meddelanden när:

* Specifika dataändringar på resurser i din Azure-prenumeration som omfattar ofta enskilda resursgrupper eller resurser. Du kanske exempelvis vill meddelas när alla virtuella datorer i myProductionResourceGroup tas bort. Eller så kanske du vill få ett meddelande om eventuella nya roller har tilldelats en användare i din prenumeration.
* En service health-händelse inträffar. Service health-händelser innehåller aviseringar incidenter och underhållshändelser som gäller för resurser i din prenumeration.

I båda fallen övervakar en aktivitetsloggavisering endast för händelser i prenumerationen som aviseringen har skapats.

Du kan konfigurera en aktivitetsloggavisering baserat på översta egenskaper i JSON-objekt för en händelse i aktivitetsloggen. Portalen visar dock de vanligaste alternativen:

- **Kategori**: administration, tjänstens hälsotillstånd, automatisk skalning och rekommendation. Mer information finns i [översikt över Azure-aktivitetsloggen](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Läs mer om service health-händelser i [ta emot aviseringar för aktivitetsloggar för tjänstmeddelanden](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Resursgrupp**
- **Resurs**
- **Resurstyp**
- **Åtgärdens namn**: The Resource Manager Role-Based Access Control åtgärdens namn.
- **Nivå**: allvarlighetsgraden för händelse (utförlig, information, varning, fel eller kritiskt).
- **Status för**: status för händelsen, vanligtvis igång, misslyckades eller lyckades.
- **Händelsen initierad av**: kallas även ”anroparen”. E-postadress eller Azure Active Directory-ID för användaren som utförde åtgärden.

> [!NOTE]
> När kategorin är ”administrativa” måste du ange minst en av de föregående kriterierna i aviseringen. Du kan inte skapa en avisering som aktiveras varje gång en händelse skapas i aktivitetsloggarna.

När en aktivitetsloggavisering är aktiverad, används en åtgärdsgrupp för att generera åtgärder eller aviseringar. En åtgärdsgrupp är en återanvändbar uppsättning meddelande olika mottagare, till exempel e-postadresser, telefonnummer för webhook-URL: er eller SMS. Mottagarna kan refereras från flera aviseringar centralisera och gruppera dina meddelandekanaler. När du definierar din aktivitetsloggsavisering har du två alternativ. Du kan:

* Använd en befintlig åtgärdsgrupp i din aktivitetsloggsavisering.
* Skapa en ny åtgärdsgrupp.

Läs mer om åtgärdsgrupper i [skapa och hantera åtgärdsgrupper i Azure-portalen](monitoring-action-groups.md).

Läs mer om service health meddelanden i [ta emot varningar för aktivitetsloggar för health tjänstmeddelanden](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-classic-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Skapa en avisering (klassisk) på en händelse i aktivitetsloggen med en ny åtgärdsgrupp med hjälp av Azure portal
1. I den [portal](https://portal.azure.com)väljer **övervakaren**.

    ![”Övervakningstjänsten”](./media/monitoring-activity-log-alerts/home-monitor.png)
1. I den **aktivitetsloggen** väljer **aviseringar (klassisk)**.

    ![Fliken ”aviseringar”](./media/monitoring-activity-log-alerts/alerts-blades.png)
1. Välj **Lägg till aktivitetsloggavisering**, och Fyll i fälten.

1. Ange ett namn i den **namn på aktivitetsloggavisering** och väljer en **beskrivning**.

    ![Kommandot ”Lägg till aktivitetsloggavisering”](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

1. Den **prenumeration** rutan autofills med din aktuella prenumeration. Den här prenumerationen är där åtgärdsgruppen sparas. Aviseringen resursen har distribuerats till den här prenumerationen och övervakar händelser i aktivitetsloggen därifrån.

    ![Dialogrutan ”Lägg till aktivitetsloggavisering”](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

1. Välj den **resursgrupp** i aviseringen resursen har skapats. Detta är inte den resursgrupp som övervakas av aviseringen. Det är den resursgrupp där aviseringen resursen finns.

1. Alternativt kan du välja en **händelsekategori** att ändra de ytterligare filter som visas. För administrativa händelser filtren är **resursgrupp**, **Resource**, **resurstyp**, **Åtgärdsnamnet**, **Nivå**, **Status**, och **händelsen initieras av**. Värdena identifierar vilka händelser som den här aviseringen ska övervaka.

    >[!NOTE]
    >Du måste ange minst en av de föregående kriterierna i aviseringen. Du kan inte skapa en avisering som aktiveras varje gång en händelse skapas i aktivitetsloggarna.
    >
    >

1. Ange ett namn i den **namn på åtgärdsgrupp** och ange ett namn i den **kortnamnet** box. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

1.  Definiera en lista med åtgärder genom att tillhandahålla åtgärdens:

    a. **Namn på**: Ange åtgärdens namn, alias eller identifierare.

    b. **Åtgärdstyp**: Välj SMS, e-post eller webhook.

    c. **Information om**: baserat på typen av, ange ett telefonnummer, e-postadress eller webhook URI.

1.  Välj **OK** att skapa aviseringen.

Aviseringen tar några minuter att sprida fullständigt och bli aktiv. Utlöser när nya händelser som matchar aviseringens villkor.

Mer information finns i [förstå webhook-schema som används i aktivitetsloggaviseringar](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Åtgärdsgrupp som definierats i de här stegen är återanvändbara som en befintlig åtgärdsgrupp för alla framtida varningsdefinitioner.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Skapa en avisering på en händelse i aktivitetsloggen för en befintlig åtgärdsgrupp med hjälp av Azure portal
1. Följ steg 1 till och med 7 i föregående avsnitt för att skapa din aktivitetsloggsavisering.

1. Under **meddela via**väljer den **befintliga** knappen för åtgärden grupp. Välj en befintlig åtgärdsgrupp i listan.

1. Välj **OK** att skapa aviseringen.

Aviseringen tar några minuter att sprida fullständigt och bli aktiv. Utlöser när nya händelser som matchar aviseringens villkor.

## <a name="manage-your-alerts"></a>Hantera aviseringar

När du skapar en avisering, är det visas i avsnittet aviseringar i Monitor-bladet. Välj den avisering du vill hantera till:

* Redigera den.
* Ta bort den.
* Inaktivera eller aktivera den, om du vill att tillfälligt stoppa eller återuppta tar emot meddelanden om aviseringen.

## <a name="next-steps"></a>Nästa steg
- Hämta en [översikt över aviseringar](monitoring-overview-alerts.md).
- Lär dig mer om [meddelande hastighetsbegränsning](monitoring-alerts-rate-limiting.md).
- Granska den [avisering webhook för aktivitetslogg](monitoring-activity-log-alerts-webhook.md).
- Läs mer om [åtgärdsgrupper](monitoring-action-groups.md).  
- Lär dig mer om [service health meddelanden](monitoring-service-notifications.md).
- Skapa en [aktivitetsloggavisering att övervaka alla Autoskala motorn åtgärder för din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Skapa en [aktivitetsloggavisering att övervaka alla misslyckade Autoskala skala-/ skalbar åtgärder för din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
