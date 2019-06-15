---
title: Azure per VM vCPU-kvoten öka begäranden | Microsoft Docs
description: öka begäranden per vCPU-kvoten för virtuell dator
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: f921b4a95c1b0cfb29d84c0bacc17d268af6e6c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082825"
---
# <a name="vm-series-vcpu-limit-increase"></a>VM-serie vCPU-gräns

Du kan nu begära en ökning via **hjälp + Support** bladet eller **användningar + kvot** -bladet i portalen. 

## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Begär per VM vCPU-kvot på prenumerationen på objektnivå med den **hjälp + Support** bladet

Följ anvisningarna nedan för att skapa en supportbegäran via Azures 'hjälp + Support-bladet i Azure-portalen. 

1. Från https://portal.azure.com väljer **hjälp + Support**.

![Hjälp + Support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Välj **Ny supportbegäran**. 

![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Välj i listrutan Typ av problem **begränsningar för tjänsten och -prenumeration (kvoter)** .

![Utfärda typ listrutan](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Välj den prenumeration som behöver en ökad kvot.

![Välj en prenumeration newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Välj **Compute - VM (kärnor-virtuella processorer) prenumerationsgränsen ökar** i **kvottypen** listrutan. 

![Välj typ av kvot](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. I **Probleminformation**, ger ytterligare information för att bearbeta din begäran genom att klicka på **ger information om**.

![Ange information](./media/resource-manager-core-quotas-request/provide-details.png)

7. I den **kvotinformation** panelen, Välj distributionsmodell och välj en plats.

![Kvotinformation DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Välj den **SKU-familjerna** som kräver en ökning. 

![SKU-familj](./media/resource-manager-core-quotas-request/sku-family.png)

9. Ange de nya gränserna som du vill ha för prenumerationen. Avmarkera SKU: N i familjen SKU-listrutan för att ta bort en rad eller klicka på ikonen borttagna objekt ”x”. När du har angett önskad kvot för varje SKU-familj, klickar du på **spara och fortsätt** på panelen kvot information om du vill fortsätta skapa stöd för begäran.

![Nya gränserna](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Begäran per VM vCPU-kvot på prenumerationen på med **användningar + kvot** bladet

Följ anvisningarna nedan använder för att skapa en supportbegäran via Azures ”användning + kvoter” bladet som är tillgängliga i Azure-portalen. 

1. Från https://portal.azure.com väljer **prenumerationer**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

![Välj en prenumeration](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Välj **användning + kvoter**

![Välj användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. I det övre högra hörnet väljer **öka**.

![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

5. Välj **beräknings-VM (kärnor-virtuella processorer) prenumerationsgränsen ökar** som typ av citattecken. 

![Fyll i formuläret](./media/resource-manager-core-quotas-request/forms.png)
   
6. I den **kvotinformation** panelen, Välj distributionsmodell och välj en plats.

![Bladet för kvot-Problem](./media/resource-manager-core-quotas-request/problemstep.png)

7. Välj den **SKU-familjerna** som kräver en ökning.

![SKU-serier som valts](./media/resource-manager-core-quotas-request/sku-family.png)

8. Ange de nya gränserna som du vill ha för prenumerationen. Avmarkera SKU: N i familjen SKU-listrutan för att ta bort en rad eller klicka på ikonen borttagna objekt ”x”. När du har angett önskad kvot för varje SKU-familj, klickar du på **spara och fortsätt** på sidan problemet steg om du vill fortsätta skapa stöd för begäran.

![Ny kvotförfrågan-SKU](./media/resource-manager-core-quotas-request/new-limits.png)

