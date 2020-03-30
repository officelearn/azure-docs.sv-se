---
title: Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen
description: Med hälsomeddelanden för tjänsten kan du visa tjänsthälsomeddelanden som publicerats av Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748647"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen

Tjänsthälsomeddelanden publiceras av Azure-infrastrukturen i [Azure-aktivitetsloggen](../azure-monitor/platform/platform-logs-overview.md).  Meddelandena innehåller information om resurserna under prenumerationen. Med tanke på den eventuellt stora mängden information som lagras i aktivitetsloggen finns det ett separat användargränssnitt som gör det enklare att visa och ställa in aviseringar om tjänsthälsomeddelanden. 

Hälsomeddelanden för tjänster kan vara informativa eller användbara, beroende på klass.

Mer information om de olika klasserna av hälsomeddelanden för tjänster finns i [Egenskaper för hälsomeddelanden för tjänsten](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visa hälsomeddelanden för tjänsten i Azure-portalen

1. Välj **Övervaka**i [Azure-portalen](https://portal.azure.com).

    ![Skärmbild av Azure Portal-menyn, med Övervakaren markerad](./media/service-notifications/home-monitor.png)

    Azure Monitor samlar alla dina övervakningsinställningar och data i en konsoliderad vy. Först öppnas den i avsnittet **Aktivitetslogg**.

1. Välj **Aviseringar**.

    ![Skärmbild av monitoraktivitetsloggen, med aviseringar valda](./media/service-notifications/service-health-summary.png)

1. Välj **+Lägg till aktivitetsloggavisering**och ställ in en avisering för att säkerställa att du meddelas för framtida servicemeddelanden. Mer information finns i [Skapa aktivitetsloggaviseringar för tjänstmeddelanden](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [aktivitetsloggvarningar](../azure-monitor/platform/activity-log-alerts.md).
