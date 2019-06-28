---
title: Azure regionalkvoten öka begäranden | Microsoft Docs
description: Förfrågningar om att öka regionala
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7ac6dce9aec1c363fa9772caabad9ce542d43888
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310656"
---
# <a name="total-regional-vcpu-limit-increase"></a>Utökad gräns för totalt antal regionala vCPU 

Resource Manager vCPU-kvoter för virtuella datorer och VM-skalningsuppsättningar tillämpas på två nivåer för varje prenumeration i varje region. 

Den första nivån är den **gränsen för totalt antal regionala virtuella processorer** (över alla VM-serien), och det andra lagret är den **per VM-serie virtuella processorer begränsa** (till exempel D-serien virtuella processorer). Varje gång en ny virtuell dator är att distribueras, får summan av nya och befintliga virtuella processorer användning för VM serien inte överskrida vCPU-kvoten som godkänts för den specifika VM-serie. Dessutom kan får den antal för totalt antal nya och befintliga virtuella processorer som distribueras över alla VM-serier inte överstiga Totalt antal regionala kvoten för virtuella processorer som godkänts för prenumerationen. Om något av dessa kvoter överskrids får inte VM-distributionen.
Du kan begära en ökning av virtuella processorer kvotgränsen för VM-serierna från Azure-portalen. En ökning av kvoten för VM-serie ökar automatiskt gränsen för totalt antal regionala virtuella processorer med samma belopp. 

När en ny prenumeration skapas, kanske Totalt antal regionala virtuella processorer standard inte lika med summan av standard vCPU-kvoter för alla enskilda VM-serier. Detta kan resultera i en prenumeration med tillräckligt stor kvot för varje enskild VM-serie som du vill distribuera, men inte tillräcklig kvot för totalt antal regionala virtuella processorer för alla distributioner. I det här fallet behöver du ansöka om att öka gränsen för totalt antal regionala virtuella processorer uttryckligen. Totalt antal regionala virtuella processorer gränsen får inte överskrida summan av godkända kvot för alla VM-serier för regionen.

Läs mer om kvoter på den [VM vCPU-kvoter sidan](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och [Azure-prenumeration och tjänstbegränsningar](https://aka.ms/quotalimits) sidan. 

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



