---
title: Begär en ökning i de regionala vCPU kvot gränserna för Azure | Microsoft Docs
description: Begäran om att öka regional kvot
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896745"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standard kvot: öka gränserna efter region 

Azure Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer:
* *Virtuella datorer med betala per* användning och *reserverade VM-instanser* omfattas av en *standard vCPU-kvot*.
* *Virtuella datorer för virtuella datorer* omfattas av en *vCPU kvot*. 

Standard kvoten för vCPU för betala per användning och reserverade VM-instanser tillämpas på två nivåer för varje prenumeration i varje region:
* Den första nivån är den *totala regionala virtuella processorer-gränsen* (i alla VM-serier).
* Den andra nivån är *virtuella processorer-gränsen per VM-serien* (till exempel D-serien virtuella processorer).
 
När du distribuerar en ny virtuell dator, får den totala nya och befintliga vCPU-användningen för VM-serien inte överskrida den godkända vCPU-kvoten för den aktuella VM-serien. Dessutom bör det totala antalet nya och befintliga virtuella processorer som distribueras i alla VM-serien inte överstiga den totala godkända regionala vCPU-kvoten för prenumerationen. Om någon av dessa kvoter överskrids tillåts inte VM-distributionen. 

Du kan begära en ökning av vCPU kvot gräns för VM-serien med hjälp av Azure Portal. En ökning av kvoten för VM-serien ökar automatiskt den totala regionala vCPU-gränsen med samma belopp.

När du skapar en ny prenumeration kanske det totala antalet regionala virtuella processorer inte är lika med den totala standard kvoten för vCPU för alla enskilda VM-serier. Den här avvikelsen kan resultera i en prenumeration med tillräcklig kvot för varje enskild VM-serie som du vill distribuera. Men det kanske inte finns tillräckligt med kvot för att hantera den totala regionala virtuella processorer för alla distributioner. I så fall måste du skicka en begäran om att uttryckligen öka gränsen för det totala antalet regionala virtuella processorer. Den totala regionala vCPU-gränsen får inte överskrida den totala godkända kvoten för alla VM-serier för regionen.

Mer information om standard kvoter för vCPU finns i [vCPU-kvoter för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och [begränsningar för Azure-prenumerationer och tjänster](https://aka.ms/quotalimits).

Mer information om hur du ökar vCPU-gränser för virtuella datorer finns i [punkt kvot: öka gränserna för alla VM-serier](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Du kan begära en ökning av vCPU-standardkvoten per region på något av två sätt, enligt beskrivningen i nästa avsnitt.

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>Begär en kvot ökning per region i rutan "hjälp + support"

Om du vill begära en vCPU kvot ökning per region från **Hjälp + Support** -fönstret, gör du följande: 

1. I den vänstra rutan i [Azure Portal](https://portal.azure.com)väljer du **Hjälp + Support**.

   ![Länken "Help + support"](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. I **Hjälp + Support** -fönstret väljer du **ny supportbegäran**. 

    ![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. I list rutan **typ av ärende** väljer du begränsningar för **tjänsten och prenumerationen (kvoter)** .

   ![List rutan utfärdande typ](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. I list rutan **prenumeration** väljer du den prenumeration vars kvot du vill öka.

   ![List rutan "prenumeration"](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. Välj **andra begär Anden**i list rutan **kvot typ** .

   ![List rutan "kvot typ"](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Ange följande ytterligare information i rutan **Beskrivning** i fönstret **problem information** : 

    a. För **distributions modell**anger du **Resource Manager**.  
    b. För **region**anger du din obligatoriska region (till exempel **USA, östra 2**).  
    c. För **ny gräns**anger du en ny vCPU-gräns för regionen. Värdet får inte överstiga summan av de godkända kvoterna för enskilda SKU-serier för den här prenumerationen.

    ![Fönstret "problem information"](./media/resource-manager-core-quotas-request/regional-details.png)

1. Välj **Spara och fortsätt** att skapa support förfrågan.

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>Begär en kvot ökning per region från fönstret prenumerationer

Om du vill begära en vCPU kvot ökning per region från fönstret **prenumerationer** gör du följande: 

1. I den vänstra rutan i [Azure Portal](https://portal.azure.com)väljer du **prenumerationer**.

   ![Länken "prenumerationer"](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Välj den prenumeration vars kvot du vill öka.

   ![Fönstret prenumerationer](./media/resource-manager-core-quotas-request/select-subscription.png)

1. I den vänstra rutan på sidan **\<prenumerations namn >** väljer du **användning + kvoter**.

   ![Länken "användning + kvoter"](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. Välj **begär ökning**längst upp till höger.

   ![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

1. Välj **andra begär Anden**i list rutan **kvot typ** .

   ![List rutan "kvot typ"](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Ange följande ytterligare information i rutan **Beskrivning** i fönstret **problem information** : 

    a. För **distributions modell**anger du **Resource Manager**.  
    b. För **region**anger du din obligatoriska region (till exempel **USA, östra 2**).  
    c. För **ny gräns**anger du en ny vCPU-gräns för regionen. Värdet får inte överstiga summan av de godkända kvoterna för enskilda SKU-serier för den här prenumerationen.

    ![Fönstret "problem information"](./media/resource-manager-core-quotas-request/regional-details.png)

1. Välj **Spara och fortsätt** att skapa support förfrågan.

