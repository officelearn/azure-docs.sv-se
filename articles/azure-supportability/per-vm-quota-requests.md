---
title: Kvot öknings begär Anden för Azure per VM-vCPU | Microsoft Docs
description: per VM-vCPU kvot öknings begär Anden
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 280561126186e4c70399b3a1ddd177ff4eb54400
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850048"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>Standard kvot: vCPU gräns för VM-serien per virtuell dator

Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer. Använd standard kvot **för virtuella datorer som du betalar per användning och reserverade VM-instanser** . **Virtuella datorer** som använder punkt kvoter. Standard vCPU-kvot för betala per användning och reserverade VM-instanser tillämpas på två nivåer för varje prenumeration i varje region

Den första nivån är den **totala regionala virtuella processorer-gränsen** (i alla VM-serien) och den andra nivån är **virtuella processorer-gränsen för VM-serien** (exempelvis dv3-serien virtuella processorer). När en ny virtuell dator ska distribueras måste summan av nya och befintliga virtuella processorer-användning för den virtuella dator serien inte överskrida vCPU-kvoten som har godkänts för den aktuella VM-serien. Dessutom bör det totala antalet nya och befintliga vCPU som distribueras över alla VM-serien inte överskrida den totala regionala virtuella processorer-kvoten som har godkänts för prenumerationen. Om någon av dessa kvoter överskrids, kommer distributionen av virtuella datorer inte att tillåtas.
Du kan begära en ökning av virtuella processorer kvot gräns för VM-serien från Azure Portal. En ökning av kvoten för VM-serien ökar automatiskt den totala regionala virtuella processorer-gränsen med samma belopp. 

Läs mer om standard kvoter för vCPU på [sidan för virtuella datorer vCPU kvoter](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och [sidan för Azure-prenumeration och tjänst begränsningar](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Läs mer om hur du ökar den regionala vCPU-gränsen för standard kvot [här](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Lär dig mer om att **öka gränserna för VM-vCPU** [här](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Du kan begära en ökning av **standard kvot gränser för vCPU per VM-serien** via **Hjälp + Support** -bladet eller **användnings** -och kvot bladet i portalen.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>Begär standard vCPU kvot ökning per VM-serie på prenumerations nivå med hjälp + support-bladet

Följ instruktionerna nedan för att skapa en support förfrågan via Azures hjälp + support-bladet som är tillgängligt i Azure Portal. 

Du kan också begära kvot för flera regioner genom ett enda support ärende. Mer information finns i steg 11 nedan.

1. Välj **Hjälp + Support**från https://portal.azure.com.

   ![Hjälp + Support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Välj **Ny supportbegäran**. 

     ![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. I list rutan ärende typ väljer du **tjänst-och prenumerations gränser (kvoter)** .

   ![Listruta för problem typ](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Välj den prenumeration som behöver en ökad kvot.

   ![Välj prenumerations nyheter](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Välj **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar** i list rutan för **kvot typ** . 

   ![Välj typ av kvot](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. I **problem information**anger du ytterligare information som hjälper dig att bearbeta din begäran genom att klicka på **Ange information**.

   ![Ange information](./media/resource-manager-core-quotas-request/provide-details.png)

7. Välj **distributions modell** på panelen **kvot information** och välj en **plats.**

   ![Kvot information DM](./media/resource-manager-core-quotas-request/1-7.png)

8. För den valda platsen väljer du **Ange** värde som **standard**. Du kan begära både standard-och plats kvot typer från ett enda support ärende via stöd för flera markeringar i fältet **typ** . Lär dig mer om att **öka kvot gränserna för platsen** på den här [sidan](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)

   ![SKU-familj](./media/resource-manager-core-quotas-request/1-8.png)

9. Välj de **SKU-familjer** som kräver en ökning

   ![SKU-familj](./media/resource-manager-core-quotas-request/1-9.png)

10. Ange de nya gränser som du vill ha i prenumerationen. Om du vill ta bort en rad avmarkerar du SKU: n från List rutan SKU-familj eller klickar på ikonen Ta bort x. 

   ![Nya gränser](./media/resource-manager-core-quotas-request/1-10.png)

11. Om du vill begära kvot för mer än en plats kan du kontrol lera en annan **plats** i list rutan och välja lämplig VM-typ. Det här steget förinstallerar SKU-familjer som valts för den **nya platsen och** du kan enkelt ange de nya gränser som du vill.

   ![Flera platser](./media/resource-manager-core-quotas-request/1-11.png)
   
12. När du har angett den önskade kvoten för varje SKU-familj klickar du på **Spara och fortsätter** med att skapa support förfrågan.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>Begär standard vCPU kvot ökning per VM-serie på prenumerations nivå med användnings områden och kvot blad

Följ instruktionerna nedan för att skapa en support förfrågan via Azures blad "användning + kvot" som är tillgängligt i Azure Portal.

Du kan också **begära kvot för flera regioner** genom ett enda support ärende. Mer information finns i steg 10 nedan.

1. Välj **prenumerationer**från https://portal.azure.com.

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

   ![Bladet kvot problem](./media/resource-manager-core-quotas-request/1-1-6.png)

7. För den valda platsen väljer du **Ange** värde som **standard**. Du kan begära både standard-och låg prioritets kvot typer från ett enda support ärende via stöd för flera markeringar i fältet **typ** . Lär dig mer om att **öka gränsen för virtuella processorer** på den här [sidan](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   ![SKU-serien har valts](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. Välj de **SKU-familjer** som kräver en ökning

   ![SKU-serien har valts](./media/resource-manager-core-quotas-request/1-1-8.png)

9. Ange de nya gränser som du vill ha i prenumerationen. Om du vill ta bort en rad avmarkerar du SKU: n från List rutan SKU-familj eller klickar på ikonen Ta bort x. 

   ![SKU ny kvot förfrågan](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. Om du vill begära kvot för mer än en plats kan du kontrol lera en annan **plats** i list rutan och välja lämplig VM-typ. Det här steget förinstallerar SKU-familjer som valts för den **nya platsen och** du kan enkelt ange de nya gränser som du vill.
   
    ![SKU ny kvot förfrågan](./media/resource-manager-core-quotas-request/1-1-10.png)
 
