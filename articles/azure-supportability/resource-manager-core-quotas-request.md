---
title: Azure Resource Manager-vCPU-kvoten öka begäranden | Microsoft Docs
description: Azure Resource Manager-vCPU förfrågningar om att öka kvoten
author: ganganarayanan
ms.author: gangan
ms.date: 6/13/2018
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 1be05ac7216a65922927b94d1cc4ba2a893433b2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250993"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Resource Manager vCPU förfrågningar om att öka kvoten

Resource Manager vCPU-kvoter genomdrivs på region och i familjen SKU-nivå.
Mer information om hur kvoter genomdrivs på den [Azure-prenumeration och tjänstbegränsningar](https://aka.ms/quotalimits) sidan.
Mer information om SKU-familjer, du kan jämföra kostnad och prestanda på den [prissättning för Virtual Machines](https://aka.ms/pricingcompute) sidan.

Om du vill begära en ökning i anvisningarna nedan använder för att skapa en supportbegäran via Azures ”användning + kvoter” bladet som är tillgängliga i Azure Portal. 

## <a name="request-quota-increase-at-subscription-level"></a>Kvot på prenumerationsnivån

1. Från https://portal.azure.comväljer **prenumerationer**.

   ![Prenumerationer](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

   ![Välj en prenumeration](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Välj **användning + kvoter**

   ![Välj användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. I det övre högra hörnet väljer **öka**.

   ![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

5. Steg: 1 - Välj **kärnor** som typ av citattecken. 

   ![Fyll i formuläret](./media/resource-manager-core-quotas-request/forms.png)
   
6. Steg: 2 – Välj distributionen modellera som ”Resource Manager” och välj en plats.

    ![Bladet för kvot-Problem](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Välj SKU-familjer som kräver en ökning.

    ![SKU-serier som valts](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Ange de nya gränserna som du vill ha för prenumerationen.

    ![Ny kvotförfrågan-SKU](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Avmarkera SKU: N i familjen SKU-listrutan för att ta bort en rad eller klicka på ikonen borttagna objekt ”x”.
Klicka på ”Nästa” på sidan problemet steget och fortsätta med stöd för begäran skapas när du har angett önskad kvot för varje SKU-familj.

