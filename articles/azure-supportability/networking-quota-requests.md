---
title: Nätverk gräns | Microsoft Docs
description: Gräns för nätverk
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297244"
---
# <a name="networking-limit-increase"></a>Gräns för nätverk

Om du vill visa din aktuella nätverk användning och kvoter, kan du besöka den **användningar + kvot** bladet i Azure-portalen. Du kan också använda användning [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) eller [nätverkets användning API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) att visa dina nätverksanvändning och gränser.

Du kan begära en ökning via **hjälp + Support** bladet eller **användningar + kvot** -bladet i portalen.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Kvot för nätverk i prenumerationen på objektnivå med den **hjälp + Support** bladet

Följ anvisningarna nedan för att skapa en supportbegäran via Azures 'hjälp + Support-bladet i Azure-portalen. 

1. Från https://portal.azure.com väljer **hjälp + Support**.

    ![Hjälp + Support](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Välj **Ny supportbegäran**. 

    ![Ny supportbegäran](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Välj i listrutan Typ av problem **begränsningar för tjänsten och -prenumeration (kvoter)** .

    ![Utfärda typ listrutan](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Välj den prenumeration som behöver en ökad kvot.

    ![Välj en prenumeration newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Välj **nätverk** i **kvottypen** listrutan. 

    ![Välj typ av kvot](./media/networking-quota-request/select-quota-type-network.png)

6. I **Probleminformation**, ger ytterligare information för att bearbeta din begäran genom att klicka på **ger information om**.

    ![Ange information](./media/resource-manager-core-quotas-request/provide-details.png)

7. I den **kvotinformation** väljer distribution modellen, en plats och de resurser som du vill begära en ökning av.

    ![Kvotinformation DM](./media/networking-quota-request/quota-details-network.png)

8.  Ange de nya gränserna som du vill ha för prenumerationen. Om du vill ta bort en rad, avmarkera resursen i listrutan resurs eller klicka på ikonen borttagna objekt ”x”. När du har angett önskad kvot för varje resurs, klickar du på **spara och fortsätt** på panelen kvot information om du vill fortsätta skapa stöd för begäran.

    ![Nya gränserna](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Kvot för nätverk i prenumerationen på med **användningar + kvot** bladet

Följ anvisningarna nedan använder för att skapa en supportbegäran via Azures ”användning + kvoter” bladet som är tillgängliga i Azure-portalen. 

1. Från https://portal.azure.com väljer **prenumerationer**.

    ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

    ![Välj en prenumeration](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Välj **användning + kvoter**

    ![Välj användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. I det övre högra hörnet väljer **öka**.

    ![Begär ökning](./media/resource-manager-core-quotas-request/request-increase.png)

5. Följer du stegen från och med steg 3 från den *begär nätverk kvot på abonnemangsnivå* avsnittet med hjälp av den **hjälp + Support** bladservrar

## <a name="about-networking-limits"></a>Om begränsningar för nätverk

Läs mer om begränsningar för nätverk i den [nätverksavsnittet](../azure-subscription-service-limits.md#networking-limits) av sidan gränser eller vanliga frågor och svar för nätverket gränser