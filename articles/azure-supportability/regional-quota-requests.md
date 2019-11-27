---
title: Begäran om att utöka Azure regional kvot | Microsoft Docs
description: Begäran om att öka regional kvot
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 6c2d7dc64bca00ce664dc470e7c4405d69b49779
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531523"
---
# <a name="standard-quota-regional-vcpu-limit-increase"></a>Standard kvot: storleks ökning för regionala vCPU 

Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer. Använd standard kvot **för virtuella datorer som du betalar per** användning och **reserverade VM-instanser** . **Virtuella datorer med låg prioritet** använder låg prioritets kvot. 

Standard vCPU-kvot för betala per användning och reserverade VM-instanser tillämpas på två nivåer för varje prenumeration i varje region.
 
Den första nivån är den **totala regionala virtuella processorer-gränsen** (i alla VM-serien) och den andra nivån är **virtuella processorer-gränsen för VM** -serien (till exempel D-serien virtuella processorer). När en ny virtuell dator ska distribueras måste summan av nya och befintliga virtuella processorer-användning för den virtuella dator serien inte överskrida vCPU-kvoten som har godkänts för den aktuella VM-serien. Dessutom bör det totala antalet nya och befintliga vCPU som distribueras över alla VM-serien inte överskrida den totala regionala virtuella processorer-kvoten som har godkänts för prenumerationen. Om någon av dessa kvoter överskrids, kommer distributionen av virtuella datorer inte att tillåtas. Du kan begära en ökning av virtuella processorer kvot gräns för VM-serien från Azure Portal. En ökning av kvoten för VM-serien ökar automatiskt den totala regionala virtuella processorer-gränsen med samma belopp.

När en ny prenumeration skapas får den totala regionala virtuella processorer inte vara lika med summan av standard kvoter för vCPU för alla enskilda VM-serier. Detta kan resultera i en prenumeration med tillräcklig kvot för varje enskild VM-serie som du vill distribuera, men inte tillräckligt med kvot för den totala regionala virtuella processorer för alla distributioner. I så fall måste du skicka en begäran om att öka den totala regionala virtuella processorer-gränsen explicit. Den totala regionala virtuella processorer-gränsen får inte överstiga summan av den godkända kvoten över alla VM-serier för regionen.

Läs mer om standard kvoter för vCPU på [sidan för virtuella datorer vCPU kvoter](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och sidan för [Azure-prenumeration och tjänst begränsningar](https://aka.ms/quotalimits) .

Lär dig mer om att **öka gränsen för VM-vCPU med låg prioritet** [här](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Du kan begära en ökning av **den totala regionala vCPU-gränsen för virtuella datorer** via **Hjälp + Support** -bladet eller **användnings** -och kvot bladet i portalen.

## <a name="request-standard-quota-regional-vcpu-limit-increase-at-subscription-level-using-the-help--support-blade"></a>Begär standard kvot, regional vCPU gräns ökning på prenumerations nivå med hjälp + support-bladet

Följ instruktionerna nedan för att skapa en support förfrågan via Azures hjälp + support-bladet som är tillgängligt i Azure Portal. 

1. Välj **Hjälp + Support**från https://portal.azure.com.

![Hjälp + support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Välj **Ny supportbegäran**. 

![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. I list rutan ärende typ väljer du **tjänst-och prenumerations gränser (kvoter)** .

![Listruta för problem typ](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Välj den prenumeration som behöver en ökad kvot.

![Välj prenumerations nyheter](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Välj **andra begär Anden** i list rutan **kvot typ** .

![Typ av kvot](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. I **informations** fönstret anger du ytterligare information enligt exemplet nedan, för att hjälpa att bearbeta din begäran och fortsätta med att skapa ärendet. 
    1.  **Distributions modell** – ange Resource Manager
    2.  **Begärt region** – ange den region som krävs, t. ex. USA, östra 2
    3.  **Nytt gräns värde** – ange en ny regions gräns. Detta bör inte överstiga summan av den godkända kvoten för enskilda SKU-familjer för den här prenumerationen

![Kvot information](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Begär total regional virtuella processorer-kvot ökning på prenumerations nivå med hjälp av **användnings** -och kvot bladet

Följ instruktionerna nedan för att skapa en support förfrågan via Azures blad "användning + kvot" som är tillgängligt i Azure Portal. 

1. Välj **prenumerationer**från https://portal.azure.com.

![Prenumerationer](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

![Välj prenumeration](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Välj **användning + kvoter**

![Välj användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. I det övre högra hörnet väljer du **begär ökning**.

![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

5. Välj **andra begär Anden** i list rutan **kvot typ** .

![Typ av kvot](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. I **informations** fönstret anger du ytterligare information enligt exemplet nedan, för att hjälpa att bearbeta din begäran och fortsätta med att skapa ärendet. 
    1.  **Distributions modell** – ange Resource Manager
    2.  **Begärt region** – ange den region som krävs, t. ex. USA, östra 2
    3.  **Nytt gräns värde** – ange en ny regions gräns. Detta bör inte överstiga summan av den godkända kvoten för enskilda SKU-familjer för den här prenumerationen

![Kvot information](./media/resource-manager-core-quotas-request/regional-details.png)



