---
title: Kvot öknings begär Anden för Azure per VM-vCPU | Microsoft Docs
description: per VM-vCPU kvot öknings begär Anden
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e99bdd92d4a1aab833a95943b22aaabe7f0daa1d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248664"
---
# <a name="vm-series-vcpu-limit-increase"></a>Utökad vCPU-gräns för VM-serie

Resource Manager-vCPU kvoter för virtuella datorer och skalnings uppsättningar för virtuella datorer tillämpas på två nivåer för varje prenumeration i varje region. 

Den första nivån är den **totala regionala virtuella processorer-gränsen** (i alla VM-serien) och den andra nivån är **virtuella processorer-gränsen för VM** -serien (till exempel D-serien virtuella processorer). När du vill distribuera en ny virtuell dator måste summan av den nya och befintliga virtuella processorer-användningen för den virtuella dator serien inte överskrida vCPU-kvoten som har godkänts för den aktuella VM-serien. Dessutom bör det totala antalet nya och befintliga vCPU som distribueras över alla VM-serien inte överskrida den totala regionala virtuella processorer-kvoten som har godkänts för prenumerationen. Om någon av dessa kvoter överskrids, kommer distributionen av virtuella datorer inte att tillåtas.
Du kan begära en ökning av virtuella processorer kvot gräns för VM-serien från Azure Portal. En ökning av kvoten för VM-serien ökar automatiskt den totala regionala virtuella processorer-gränsen med samma belopp. 

Läs mer om kvoter på sidan för [vCPU kvoter för virtuell dator](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och sidan för [Azure-prenumeration och tjänst begränsningar](https://aka.ms/quotalimits) . 

Nu kan du begära en ökning via bladet **Hjälp + Support** eller bladet **användnings** -och kvot i portalen. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Begäran per VM-serien vCPU kvot ökning på prenumerations nivå med **Hjälp + Support** -bladet

Följ instruktionerna nedan för att skapa en support förfrågan via Azures hjälp + support-bladet som är tillgängligt i Azure Portal. 

1. Från https://portal.azure.com väljer du **Hjälp + Support**.

   ![Hjälp + support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Välj **Ny supportbegäran**. 

     ![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. I list rutan typ av ärende väljer du **tjänst-och prenumerations gränser (kvoter)** .

   ![Listruta för ärende typ](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Välj den prenumeration som behöver en ökad kvot.

   ![Välj prenumerations nyheter](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Välj **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar** i list rutan med **kvot typer** . 

   ![Välj typ av kvot](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. I **problem information**anger du ytterligare information som hjälper dig att bearbeta din begäran genom att klicka på **Ange information**.

   ![Ange information](./media/resource-manager-core-quotas-request/provide-details.png)

7. Välj distributions modell på panelen **kvot information** och välj en plats.

   ![Kvot information DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Välj de **SKU-familjer** som kräver en ökning. 

   ![SKU-familj](./media/resource-manager-core-quotas-request/sku-family.png)

9. Ange de nya gränser som du vill ha i prenumerationen. Om du vill ta bort en rad avmarkerar du SKU: n från List rutan för SKU-serien eller klickar på ikonen ignorera x. När du har angett den önskade kvoten för varje SKU-familj klickar du på **Spara och fortsätter** med att skapa support förfrågan.

   ![Nya gränser](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Begäran per VM-serien vCPU kvot ökning på prenumerations nivå med **användnings områden och kvot** blad

Följ instruktionerna nedan för att skapa en support förfrågan via Azures blad "användning + kvot" som är tillgängligt i Azure Portal. 

1. Från https://portal.azure.com väljer du **prenumerationer**.

   ![Prenumerationer](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

   ![Välj prenumeration](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Välj **användning + kvoter**

   ![Välj användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. I det övre högra hörnet väljer du **begär ökning**.

   ![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

5. Välj **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar** som offert typ. 

   ![Fyll i formulär](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. Välj distributions modell på panelen **kvot information** och välj en plats.

   ![Bladet kvot problem](./media/resource-manager-core-quotas-request/quota-details.png)

7. Välj de **SKU-familjer** som kräver en ökning.

   ![SKU-serien har valts](./media/resource-manager-core-quotas-request/sku-family.png)

8. Ange de nya gränser som du vill ha i prenumerationen. Om du vill ta bort en rad avmarkerar du SKU: n från List rutan för SKU-serien eller klickar på ikonen ignorera x. När du har angett den önskade kvoten för varje SKU-familj klickar du på **Spara och fortsätter** på sidan problem steg och fortsätter med att skapa support förfrågan.

   ![SKU ny kvot förfrågan](./media/resource-manager-core-quotas-request/new-limits.png)
 
