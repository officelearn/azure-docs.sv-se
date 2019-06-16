---
title: Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen
description: Meddelanden om hälsostatus för tjänsten kan du visa service health meddelanden som publiceras av Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 8cf8c3eb86f55b5595ef9a1af83ea50580bf638b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069344"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen

Meddelanden om Service health publiceras via Azure-infrastrukturen. De innehåller information om resurser i din prenumeration. Dessa aviseringar är en underklass till aktiviteten logghändelser och så även finns i aktivitetsloggen. Meddelanden om hälsostatus för tjänsten kan vara information eller användbara, beroende på klassen.

Mer information om olika typer av meddelanden om hälsostatus för tjänsten finns i [hälsotillstånd meddelanden tjänstegenskaper](../../service-health/service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visa dina meddelanden om hälsostatus för tjänsten i Azure portal

1. I den [Azure-portalen](https://portal.azure.com)väljer **övervakaren**.

    ![Skärmbild av Azure portal-menyn med Övervakare som har valts](./media/service-notifications/home-monitor.png)

    Azure Monitor samlar alla dina övervakningsinställningar och -data till en sammanslagen vy. Först öppnas den i avsnittet **Aktivitetslogg**.

1. Välj **aviseringar**.

    ![Skärmbild av övervakaren aktivitetslogg, med aviseringar som har valts](./media/service-notifications/service-health-summary.png)

1. Välj **+ Lägg till aktivitetsloggavisering**, och ställa in en avisering för att se till att du får ett meddelande för framtida tjänstmeddelanden. Mer information finns i [skapa aviseringar för aktivitetsloggen för tjänstmeddelanden](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Nästa steg

* Ta emot [aviseringar när en avisering om tjänstens hälsa](../../azure-monitor/platform/alerts-activity-log-service-notifications.md) publiceras.  
* Läs mer om [aktivitetsloggaviseringar](../../azure-monitor/platform/activity-log-alerts.md).
