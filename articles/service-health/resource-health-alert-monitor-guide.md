---
title: Skapa Resource Health aviseringar med hjälp av Azure Portal
description: Skapa en avisering med Azure Portal som meddelar dig när dina Azure-resurser blir otillgängliga.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: 2ba7597283ed254b8c03e8ea58c3c7b1257250ee
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85218075"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Konfigurera resurs hälso aviseringar med hjälp av Azure Portal

Den här artikeln visar hur du ställer in aktivitets logg aviseringar för resurs hälso meddelanden med hjälp av Azure Portal.

Azure Resource Health håller dig informerad om aktuella och historiska hälso status för dina Azure-resurser. Azure Resource Health aviseringar kan meddela dig nästan i real tid när resurserna har en ändring i deras hälso status. Skapa Resource Health aviseringar program mässigt tillåta att användare skapar och anpassar aviseringar i flera.

> [!NOTE]
> Resource Health aviseringar finns för närvarande i för hands version.

Resurs hälso meddelanden lagras i [Azure aktivitets loggen](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview) med den stora mängden information som lagras i aktivitets loggen. det finns ett separat användar gränssnitt som gör det enklare att visa och konfigurera aviseringar om resurs hälso meddelanden.
Du kan få en avisering när Azure-resursen skickar resurs hälso meddelanden till din Azure-prenumeration. Du kan konfigurera aviseringen baserat på:

* Prenumerationen som påverkas.
* Resurs (er) typ (er) som påverkas.
* Den eller de resurs grupper som påverkas.
* Resurs (er) som påverkas.
* Händelse status för resurs (er) som påverkas.
* Resurs (er) påverkade status.
* Anledning (er) typ (er) av resurs (er) som påverkas.

Du kan också konfigurera vem aviseringen ska skickas till:

* Välj en befintlig åtgärds grupp.
* Skapa en ny åtgärds grupp (som kan användas för framtida aviseringar).

Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

Information om hur du konfigurerar aviserings aviseringar för resurs hälsa med hjälp av Azure Resource Manager mallar finns i [Resource Manager-mallar](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide).
Resource Health avisering med Azure Portal

## <a name="resource-health-alert-using-azure-portal"></a>Resource Health avisering med hjälp av Azure Portal

1. I Azure- [portalen](https://portal.azure.com/)väljer du **service Health**.

    ![Service Health val](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. I avsnittet **Resource Health** väljer du **Resource Health**.
3. Välj **Lägg till resurs hälso avisering** och fyll i fälten.
4. Under aviserings mål väljer du den **prenumeration**, de **resurs typer**, **resurs grupper** och den **resurs** som du vill bli aviserad om.

    ![Val av val av mål](./media/resource-health-alert-monitor-guide/alert-target.png)

5. Under aviserings villkor väljer du:
    1. **Händelse status** som du vill bli aviserad om. Allvarlighets grad för händelsen: aktiv, löst, pågår, uppdaterat
    2. Den **resurs status** som du vill bli aviserad om. Resurs status för händelsen: tillgänglig, ej tillgänglig, okänd, degraderad
    3. Den **orsaks typ** som du vill bli aviserad om. Orsaken till händelsen: plattform initierad, användare som initierade ![ varnings villkor val](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. Under definiera aviserings information anger du följande information:
    1. **Namn på varnings regel**: namnet på den nya varnings regeln.
    2. **Beskrivning**: beskrivningen av den nya varnings regeln.
    3. **Spara avisering till resurs grupp**: Välj den resurs grupp där du vill spara den nya regeln.
7. Under **Åtgärds grupp**i den nedrullningsbara menyn anger du den åtgärds grupp som du vill tilldela till den nya varnings regeln. Du kan också [skapa en ny åtgärds grupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) och tilldela den till den nya regeln. Om du vill skapa en ny grupp väljer du + **ny grupp**.
8. Om du vill aktivera reglerna efter att du har skapat dem väljer du **Ja** för alternativet **Aktivera regel vid skapande** .
9. Välj **Skapa aviseringsregel**.

Den nya varnings regeln för aktivitets loggen skapas och ett bekräftelse meddelande visas i det övre högra hörnet i fönstret.
Du kan aktivera, inaktivera, redigera eller ta bort en regel. Läs mer om [hur du hanterar aktivitets logg regler](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#view-and-manage-in-the-azure-portal).

## <a name="next-steps"></a>Nästa steg

Läs mer om Resource Health:

* [Översikt över Azure Resource Health](Resource-health-overview.md)
* [Resurstyper och hälsokontroller är tillgängliga genom Azure Resource Health](resource-health-checks-resource-types.md)

Skapa Service Health aviseringar:

* [Konfigurera aviseringar för Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
* [Händelse schema för Azure aktivitets logg](../azure-monitor/platform/activity-log-schema.md)
* [Konfigurera resurs hälso aviseringar med Resource Manager-mallar](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)