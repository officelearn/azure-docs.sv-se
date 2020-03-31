---
title: Använda Azure-portalen för att övervaka en hanterad app
description: Visar hur du använder Azure-portalen för att övervaka tillgänglighet och aviseringar för ett hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: afe78dd00ecebdc54b6d73c4c8324729e117d95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651753"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Övervaka en distribuerad instans av ett hanterat program

När du har distribuerat ett hanterat program till din Azure-prenumeration kanske du vill kontrollera status för programmet. Den här artikeln visar alternativ i Azure-portalen för att kontrollera status. Du kan övervaka tillgängligheten för resurserna i det hanterade programmet. Du kan också ställa in och visa aviseringar.

## <a name="view-resource-health"></a>Visa information om resurshälsa

1. Välj din hanterade programinstans.

   ![Välj hanterat program](./media/monitor-managed-application-portal/select-managed-application.png)

1. Välj **Resurshälsa**.

   ![Välj resurshälsa](./media/monitor-managed-application-portal/select-resource-health.png)

1. Visa tillgängligheten för resurserna i det hanterade programmet.

   ![Visa information om resurshälsa](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Visa aviseringar

1. Välj **Aviseringar**.

   ![Välj aviseringar](./media/monitor-managed-application-portal/select-alerts.png)

1. Om du har konfigurerat varningsregler visas information om aviseringar som har höjts.

   ![Visa aviseringar](./media/monitor-managed-application-portal/view-alerts.png)

1. Om du vill lägga till varningsregler väljer du **+ Ny varningsregel**.

   ![Skapa avisering](./media/monitor-managed-application-portal/create-new-alert.png)

Du kan skapa aviseringar för din hanterade programinstans eller resurserna i det hanterade programmet. Information om hur du skapar aviseringar finns [i Översikt över aviseringar i Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Nästa steg

* Exempel på hanterade program finns i [Exempel på projekt för Azure-hanterade program](sample-projects.md).
* Information om hur du distribuerar ett hanterat program finns i [Distribuera tjänstkatalogapp via Azure Portal](deploy-service-catalog-quickstart.md).