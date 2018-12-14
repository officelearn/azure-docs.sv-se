---
title: Övervaka ett hanterat program med hjälp av Azure portal | Microsoft Docs
description: Visar hur du använder Azure-portalen för att övervaka tillgänglighet och aviseringar för ett hanterat program.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 754d2a246a86585e9f05f8a070c51e158f73affd
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342603"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Övervaka en distribuerad instans av ett hanterat program

När du har distribuerat ett hanterat program på Azure-prenumerationen kan du kontrollera status för programmet. Den här artikeln visar alternativ i Azure-portalen för att kontrollera status. Du kan övervaka tillgängligheten för resurser i det hanterade programmet. Du kan också ställa in och visa aviseringar.

## <a name="view-resource-health"></a>Visa information om resurshälsa

1. Välj din instans av hanterade programmet.

   ![Välj hanterade program](./media/monitor-managed-application-portal/select-managed-application.png)

1. Välj **Resurshälsa**.

   ![Välj resurshälsa](./media/monitor-managed-application-portal/select-resource-health.png)

1. Visa tillgängligheten för resurser i det hanterade programmet.

   ![Visa information om resurshälsa](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Visa aviseringar

1. Välj **aviseringar**.

   ![Välj aviseringar](./media/monitor-managed-application-portal/select-alerts.png)

1. Om du har Varningsregler konfigurerats kan se du information om aviseringar som har genererats.

   ![Visa aviseringar](./media/monitor-managed-application-portal/view-alerts.png)

1. Om du vill lägga till Varningsregler, Välj **+ ny aviseringsregel**.

   ![Skapa avisering](./media/monitor-managed-application-portal/create-new-alert.png)

Du kan skapa aviseringar för din instans av hanterade programmet eller resurser i det hanterade programmet. Information om hur du skapar aviseringar finns i [översikt över aviseringar i Microsoft Azure](../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Nästa steg

* Hanterat program exempel finns i [exempelprojekten för Azure hanterade program](sample-projects.md).
* Om du vill distribuera ett hanterat program [distribuera service catalog-app via Azure-portalen](deploy-service-catalog-quickstart.md).