---
title: Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen
description: Visa dina meddelanden om tjänst hälsa i Azure Portal. Meddelanden om tjänst hälsa publiceras av Azure-infrastrukturen i aktivitets loggen i Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 615d08b6a04aef9e8ef2033154da8ff8caeebe04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967780"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen

Meddelanden om tjänst hälsa publiceras av Azure-infrastrukturen i [aktivitets loggen i Azure](../azure-monitor/platform/platform-logs-overview.md).  Meddelandena innehåller information om resurserna i din prenumeration. Med tanke på den stora mängden information som lagras i aktivitets loggen, finns det ett separat användar gränssnitt för att göra det enklare att visa och konfigurera aviseringar om meddelanden om tjänstens hälsa. 

Meddelanden om tjänst hälsa kan vara informations-eller åtgärds bara, beroende på klassen.

Mer information om de olika klasserna för meddelanden om tjänst hälsa finns i [Egenskaper för tjänst hälso aviseringar](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visa dina meddelanden om tjänst hälsa i Azure Portal

1. I [Azure Portal](https://portal.azure.com)väljer du **övervaka**.

    ![Skärm bild av Azure Portal menyn med övervaka valt](./media/service-notifications/home-monitor.png)

    Azure Monitor sammanför alla övervaknings inställningar och data i en konsol IDE rad vy. Först öppnas den i avsnittet **Aktivitetslogg**.

1. Välj **aviseringar**.

    ![Skärm bild av aktivitets loggen övervakare med valda aviseringar](./media/service-notifications/service-health-summary.png)

1. Välj **+ Lägg till aktivitets logg avisering**och konfigurera en avisering för att se till att du får ett meddelande om framtida tjänst meddelanden. Mer information finns i [skapa aktivitets logg aviseringar för tjänst meddelanden](./alerts-activity-log-service-notifications-portal.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [aktivitets logg aviseringar](../azure-monitor/platform/activity-log-alerts.md).
