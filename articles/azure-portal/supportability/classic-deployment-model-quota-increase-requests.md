---
title: Klassisk Azure-distributions modell | Microsoft Docs
description: Klassisk Azure-distributions modell
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: d8f4bf04251347c44ea1692cfdda2602e23117f4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897057"
---
# <a name="classic-deployment-model"></a>Klassisk distributionsmodell

Den klassiska distributions modellen är den äldre generationens Azure-distributions modell. Den tillämpar en global vCPU kvot gräns för virtuella datorer och skalnings uppsättningar för virtuella datorer. Den klassiska distributions modellen rekommenderas inte längre och har nu ersatts av Resource Manager-modellen. 

Mer information om de här två distributions modellerna och fördelarna med att använda Resource Manager finns på sidan [Resource Manager och klassisk distribution](../../azure-resource-manager/management/deployment-models.md) .
 
När en ny prenumeration skapas, tilldelas en standard kvot för virtuella processorer. När en ny virtuell dator ska distribueras med den klassiska distributions modellen får summan av nya och befintliga vCPU-användningar i alla regioner inte överskrida vCPU-kvoten som har godkänts för den klassiska distributions modellen.
 
Läs mer om kvoter på sidan för [Azure-prenumeration och tjänst begränsningar](https://aka.ms/quotalimits) .

Du kan begära en ökning av kvot gränsen för vCPU för den klassiska distributions modellen via bladet "hjälp + support" eller "användnings + kvot" i portalen.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Begäran per VM-serien vCPU kvot ökning på prenumerations nivå med **Hjälp + Support** -bladet

Följ instruktionerna nedan för att skapa en support förfrågan via Azures hjälp + support-bladet som är tillgängligt i Azure Portal. 

1. Välj **Hjälp + Support**från https://portal.azure.com.

   ![Hjälp + Support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
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

7. Välj klassisk på panelen **kvot information** och välj en plats.

   ![Kvot information DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Välj de **SKU-familjer** som kräver en ökning. 

   ![SKU-familj](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Ange de nya gränser som du vill ha i prenumerationen. Om du vill ta bort en rad avmarkerar du SKU: n från List rutan för SKU-serien eller klickar på ikonen ignorera x. När du har angett den önskade kvoten för varje SKU-familj klickar du på **Spara och fortsätter** med att skapa support förfrågan.

   ![Nya gränser](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Begäran per VM-serien vCPU kvot ökning på prenumerations nivå med **användnings områden och kvot** blad

Följ instruktionerna nedan för att skapa en support förfrågan via Azures blad "användning + kvot" som är tillgängligt i Azure Portal. 

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
   
6. I **problem information**anger du ytterligare information som hjälper dig att bearbeta din begäran genom att klicka på **Ange information**.

   ![Ange information](./media/resource-manager-core-quotas-request/provide-details.png)

7. Välj klassisk på panelen **kvot information** och välj en plats.

   ![Kvot information DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Välj de **SKU-familjer** som kräver en ökning. 

   ![SKU-familj](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Ange de nya gränser som du vill ha i prenumerationen. Om du vill ta bort en rad avmarkerar du SKU: n från List rutan för SKU-serien eller klickar på ikonen ignorera x. När du har angett den önskade kvoten för varje SKU-familj klickar du på **Spara och fortsätter** med att skapa support förfrågan.

   ![Nya gränser](./media/resource-manager-core-quotas-request/new-limits-classic.png)

