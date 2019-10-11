---
title: Ökning av nätverks gräns | Microsoft Docs
description: Höjning av nätverksgräns
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 278e9ff68fa20a0a99a6447bb4cf7ac7fddbfb7b
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249118"
---
# <a name="networking-limit-increase"></a>Höjning av nätverksgräns

Om du vill visa din aktuella nätverks användning och kvot kan du gå till bladet **användning och kvot** i Azure Portal. Du kan också använda användnings- [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) eller [nätverks användnings-API: et](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) för att Visa nätverks användning och-gränser.

Du kan begära en ökning via bladet **Hjälp + Support** eller **användnings** -och kvot bladet i portalen.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Begär nätverks kvot öka på prenumerations nivå med **Hjälp + Support** -bladet

Följ instruktionerna nedan för att skapa en support förfrågan via Azures hjälp + support-bladet som är tillgängligt i Azure Portal. 

1. Från https://portal.azure.com väljer du **Hjälp + Support**.

    ![Hjälp + support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Välj **Ny supportbegäran**. 

    ![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. I list rutan typ av ärende väljer du **tjänst-och prenumerations gränser (kvoter)** .

    ![Listruta för ärende typ](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Välj den prenumeration som behöver en ökad kvot.

    ![Välj prenumerations nyheter](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Välj **nätverk** i list rutan med **kvot typer** . 

    ![Välj typ av kvot](./media/networking-quota-request/select-quota-type-network.png)

6. I **problem information**anger du ytterligare information som hjälper dig att bearbeta din begäran genom att klicka på **Ange information**.

    ![Ange information](./media/resource-manager-core-quotas-request/provide-details.png)

7. I panelen **kvot information** väljer du distributions modell, en plats och de resurser som du vill begära en ökning av.

    ![Kvot information DM](./media/networking-quota-request/quota-details-network.png)

8.  Ange de nya gränser som du vill ha i prenumerationen. Om du vill ta bort en rad avmarkerar du resursen i list rutan resurs eller klickar på ikonen Ta bort x. När du har angett önskad kvot för varje resurs klickar du på **Spara och fortsätter** med att fortsätta med att skapa support förfrågan.

    ![Nya gränser](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Begär nätverks kvot öka på prenumerations nivå med **användnings områden och kvot** blad

Följ instruktionerna nedan för att skapa en support förfrågan via Azures blad "användning + kvot" som är tillgängligt i Azure Portal. 

1. Från https://portal.azure.com väljer du **prenumerationer**.

    ![Prenumerationer](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

    ![Välj prenumeration](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Välj **användning + kvoter**

    ![Välj användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. I det övre högra hörnet väljer du **begär ökning**.

    ![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

5. Följ stegen som börjar med steg 3 i avsnittet om *ökad kvot för nätverks kvot på prenumerations nivå* i avsnittet **Hjälp + Support** bladet

## <a name="about-networking-limits"></a>Om nätverks begränsningar

Mer information om nätverks begränsningar finns i [avsnittet nätverk](../azure-subscription-service-limits.md#networking-limits) på sidan gränser eller vanliga frågor och svar om nätverks begränsningar