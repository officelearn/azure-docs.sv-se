---
title: Azure Resource Manager-vCPU-kvoten öka begäranden | Microsoft Docs
description: Azure Resource Manager-vCPU förfrågningar om att öka kvoten
author: sowmyavenkat86
ms.author: svenkat
ms.date: 05/09/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9d19d1a993d574777aa630b9c58f2472b94716cd
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479402"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Resource Manager vCPU förfrågningar om att öka kvoten

Resource Manager vCPU-kvoter för virtuella datorer och VM-skalningsuppsättningar tillämpas på två nivåer för varje prenumeration i varje region. 

Den första nivån är gränsen för totalt antal regionala virtuella processorer (för alla VM-serier) och det andra lagret är VM-serien virtuella processorer gränsen (till exempel D-serien virtuella processorer). Varje gång en ny virtuell dator är att distribueras, summan av nya och befintliga virtuella processorer användning för VM serien får inte överstiga vCPU-kvoten som godkänts för den specifika VM-serien ytterligare, det antal för totalt antal virtuella processorer som distribueras över alla VM-serier får inte överstiga kvoten för totalt antal regionala virtuella processorer  godkänt för prenumerationen. Om något av dessa kvoter överskrids får inte VM-distributionen.
Du kan begära en ökning av virtuella processorer kvotgränsen för VM-serierna från Azure-portalen. En ökning av kvoten för VM-serie ökar automatiskt gränsen för totalt antal regionala virtuella processorer med samma belopp. 

När en ny prenumeration skapas, totalt antal regionala virtuella processorer är inte lika med summan av standard vCPU-kvoter för alla enskilda VM-serien detta kan resultera i en prenumeration med tillräckligt stor kvot för varje enskild VM-serie som du vill distribuera standard , men prenumerationen har inte tillräcklig kvot för totalt antal regionala virtuella processorer för alla distributioner. I det här fallet behöver du ansöka om att öka den totala kvot för region gränsen uttryckligen. Totalt antal regionala virtuella processorer gränsen får inte överskrida summan av godkända kvot för alla VM-serier för regionen.

Läs mer om kvoter på den [VM vCPU-kvoter sidan](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och [Azure-prenumeration och tjänstbegränsningar](https://aka.ms/quotalimits) sidan. 

Du kan nu begära en ökning via **hjälp + Support** bladet eller **användningar + kvot** -bladet i portalen. 

## <a name="request-quota-increase-using-the-help--support-blade"></a>Begäran kvot ökning med den **hjälp + Support** bladet

Följ anvisningarna nedan för att skapa en supportbegäran via Azures 'hjälp + Support-bladet i Azure-portalen. 

1. Från https://portal.azure.comväljer **hjälp + Support**.

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


## <a name="request-quota-increase-at-subscription-level-using-usages--quota"></a>Kvot på abonnemangsnivå med hjälp av användningar + kvot

Följ anvisningarna nedan använder för att skapa en supportbegäran via Azures ”användning + kvoter” bladet som är tillgängliga i Azure-portalen. 

1. Från https://portal.azure.comväljer **prenumerationer**.

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


