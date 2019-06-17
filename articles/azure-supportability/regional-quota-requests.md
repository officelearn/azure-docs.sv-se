---
title: Azure regionalkvoten öka begäranden | Microsoft Docs
description: Förfrågningar om att öka regionala
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 4c5524cf450959db7055ca5d032c81f79ebac077
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083085"
---
# <a name="total-regional-vcpu-limit-increase"></a>Utökad gräns för totalt antal regionala vCPU 

Du kan nu begära en ökning via **hjälp + Support** bladet eller **användningar + kvot** -bladet i portalen. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Kvot Totalt antal regionala virtuella processorer på prenumerationen på objektnivå med den **hjälp + Support** bladet

Följ anvisningarna nedan för att skapa en supportbegäran via Azures 'hjälp + Support-bladet i Azure-portalen. 

1. Från https://portal.azure.com väljer **hjälp + Support**.

![Hjälp + Support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Välj **Ny supportbegäran**. 

![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Välj i listrutan Typ av problem **begränsningar för tjänsten och -prenumeration (kvoter)** .

![Utfärda typ listrutan](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Välj den prenumeration som behöver en ökad kvot.

![Välj en prenumeration newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Välj **övriga begäranden** i **kvottypen** listrutan.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. I **information** fönstret innehåller ytterligare information enligt exemplet nedan, för att bearbeta din begäran och fortsätta skapa ärende. 
    1.  **Distributionsmodell** – ange Resource Manager
    2.  **Efterfrågade regionen** – ange den nödvändiga regionen t.ex. östra USA 2
    3.  **Ny gräns värdet** – ange nya region gränsen. Detta får inte överstiga summan av godkända kvot för enskilda SKU-serier för den här prenumerationen

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Kvot Totalt antal regionala virtuella processorer på prenumerationen på objektnivå med den **användningar + kvot** bladet

Följ anvisningarna nedan använder för att skapa en supportbegäran via Azures ”användning + kvoter” bladet som är tillgängliga i Azure-portalen. 

1. Från https://portal.azure.com väljer **prenumerationer**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

![Välj en prenumeration](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Välj **användning + kvoter**

![Välj användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. I det övre högra hörnet väljer **öka**.

![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

5. Välj **övriga begäranden** i **kvottypen** listrutan.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. I **information** fönstret innehåller ytterligare information enligt exemplet nedan, för att bearbeta din begäran och fortsätta skapa ärende. 
    1.  **Distributionsmodell** – ange Resource Manager
    2.  **Efterfrågade regionen** – ange den nödvändiga regionen t.ex. östra USA 2
    3.  **Ny gräns värdet** – ange nya region gränsen. Detta får inte överstiga summan av godkända kvot för enskilda SKU-serier för den här prenumerationen

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



