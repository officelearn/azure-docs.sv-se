---
title: Begäran om att utöka Azure regional kvot | Microsoft Docs
description: Begäran om att öka regional kvot
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e73f22b0e617ad8f20b98c3bb0fb1647bf5fe61d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249077"
---
# <a name="total-regional-vcpu-limit-increase"></a>Utökad gräns för totalt antal regionala vCPU 

Resource Manager-vCPU kvoter för virtuella datorer och skalnings uppsättningar för virtuella datorer tillämpas på två nivåer för varje prenumeration i varje region. 

Den första nivån är den **totala regionala virtuella processorer-gränsen** (i alla VM-serien) och den andra nivån är **virtuella processorer-gränsen för VM** -serien (till exempel D-serien virtuella processorer). När du vill distribuera en ny virtuell dator måste summan av den nya och befintliga virtuella processorer-användningen för den virtuella dator serien inte överskrida vCPU-kvoten som har godkänts för den aktuella VM-serien. Dessutom bör det totala antalet nya och befintliga vCPU som distribueras över alla VM-serien inte överskrida den totala regionala virtuella processorer-kvoten som har godkänts för prenumerationen. Om någon av dessa kvoter överskrids, kommer distributionen av virtuella datorer inte att tillåtas.
Du kan begära en ökning av virtuella processorer kvot gräns för VM-serien från Azure Portal. En ökning av kvoten för VM-serien ökar automatiskt den totala regionala virtuella processorer-gränsen med samma belopp. 

När en ny prenumeration skapas får den totala regionala virtuella processorer inte vara lika med summan av standard kvoter för vCPU för alla enskilda VM-serier. Detta kan resultera i en prenumeration med tillräcklig kvot för varje enskild VM-serie som du vill distribuera, men inte tillräckligt med kvot för den totala regionala virtuella processorer för alla distributioner. I så fall måste du skicka en begäran om att öka den totala regionala virtuella processorer-gränsen explicit. Den totala regionala virtuella processorer-gränsen får inte överstiga summan av den godkända kvoten över alla VM-serier för regionen.

Läs mer om kvoter på sidan för [vCPU kvoter för virtuell dator](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och sidan för [Azure-prenumeration och tjänst begränsningar](https://aka.ms/quotalimits) . 

Nu kan du begära en ökning via bladet **Hjälp + Support** eller bladet **användnings** -och kvot i portalen. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Begär total regional virtuella processorer kvot ökning på prenumerations nivå med **Hjälp + Support** -bladet

Följ instruktionerna nedan för att skapa en support förfrågan via Azures hjälp + support-bladet som är tillgängligt i Azure Portal. 

1. Från https://portal.azure.com väljer du **Hjälp + Support**.

![Hjälp + support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Välj **Ny supportbegäran**. 

![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. I list rutan typ av ärende väljer du **tjänst-och prenumerations gränser (kvoter)** .

![Listruta för ärende typ](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Välj den prenumeration som behöver en ökad kvot.

![Välj prenumerations nyheter](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Välj **andra begär Anden** i list rutan med **kvot typer** .

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. I **informations** fönstret anger du ytterligare information enligt exemplet nedan, för att hjälpa att bearbeta din begäran och fortsätta med att skapa ärendet. 
    1.  **Distributions modell** – ange Resource Manager
    2.  **Begärt region** – ange den region som krävs, t. ex. USA, östra 2
    3.  **Nytt gräns värde** – ange en ny regions gräns. Detta bör inte överstiga summan av den godkända kvoten för enskilda SKU-familjer för den här prenumerationen

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Begär total regional virtuella processorer-kvot ökning på prenumerations nivå med hjälp av **användnings** -och kvot bladet

Följ instruktionerna nedan för att skapa en support förfrågan via Azures blad "användning + kvot" som är tillgängligt i Azure Portal. 

1. Från https://portal.azure.com väljer du **prenumerationer**.

![Prenumerationer](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

![Välj prenumeration](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Välj **användning + kvoter**

![Välj användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. I det övre högra hörnet väljer du **begär ökning**.

![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

5. Välj **andra begär Anden** i list rutan med **kvot typer** .

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. I **informations** fönstret anger du ytterligare information enligt exemplet nedan, för att hjälpa att bearbeta din begäran och fortsätta med att skapa ärendet. 
    1.  **Distributions modell** – ange Resource Manager
    2.  **Begärt region** – ange den region som krävs, t. ex. USA, östra 2
    3.  **Nytt gräns värde** – ange en ny regions gräns. Detta bör inte överstiga summan av den godkända kvoten för enskilda SKU-familjer för den här prenumerationen

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



