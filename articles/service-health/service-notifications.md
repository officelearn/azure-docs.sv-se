---
title: Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen
description: Meddelanden om hälsostatus för tjänsten kan du visa service health meddelanden som publiceras av Microsoft Azure.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 6/27/2019
ms.subservice: ''
ms.openlocfilehash: d2e18ae28e79590cb04ee0045341ea817be4a3bc
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538359"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen

Meddelanden om Service health publiceras via Azure-infrastrukturen till den [Azure-aktivitetsloggen](../azure-monitor/platform/activity-logs-overview.md).  Aviseringarna innehåller information om resurser i din prenumeration. Beroende eventuellt stor mängd information som lagras i aktivitetsloggen finns ett gränssnitt att göra det enklare att visa och konfigurera aviseringar för tjänstmeddelanden hälsotillstånd. 

Meddelanden om hälsostatus för tjänsten kan vara information eller användbara, beroende på klassen.

Mer information om olika typer av meddelanden om hälsostatus för tjänsten finns i [hälsotillstånd meddelanden tjänstegenskaper](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visa dina meddelanden om hälsostatus för tjänsten i Azure portal

1. I den [Azure-portalen](https://portal.azure.com)väljer **övervakaren**.

    ![Skärmbild av Azure portal-menyn med Övervakare som har valts](./media/service-notifications/home-monitor.png)

    Azure Monitor samlar alla dina övervakningsinställningar och -data till en sammanslagen vy. Först öppnas den i avsnittet **Aktivitetslogg**.

1. Välj **aviseringar**.

    ![Skärmbild av övervakaren aktivitetslogg, med aviseringar som har valts](./media/service-notifications/service-health-summary.png)

1. Välj **+ Lägg till aktivitetsloggavisering**, och ställa in en avisering för att se till att du får ett meddelande för framtida tjänstmeddelanden. Mer information finns i [skapa aviseringar för aktivitetsloggen för tjänstmeddelanden](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [aktivitetsloggaviseringar](../azure-monitor/platform/activity-log-alerts.md).
