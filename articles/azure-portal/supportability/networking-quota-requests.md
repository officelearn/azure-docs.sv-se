---
title: Ökning av gräns för nät | Microsoft-dokument
description: Höjning av nätverksgräns
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547813"
---
# <a name="networking-limit-increase"></a>Höjning av nätverksgräns

Använd [Azure-portalen](https://portal.azure.com) för att öka nätverkskvoten.

Om du vill visa din nuvarande nätverksanvändning och kvot i Azure-portalen öppnar du prenumerationen och väljer sedan **Användningar + kvoter**. Du kan också använda följande alternativ för att visa nätverksanvändning och begränsningar.

* [Användning CLI](/cli/azure/network#az-network-list-usages)
* [Powershell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [API:et för nätverksanvändning](/rest/api/virtualnetwork/virtualnetworks/listusage)

Du kan begära en ökning med hjälp av **hjälp + stöd** eller i **Användningar + kvoter** i portalen.

> [!Note]
> Om du vill ändra standardstorleken för **offentliga IP-prefix**väljer du **Min Public IP InterNetwork Prefix Length** i listrutan.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Begär att nätverkskvoten ökar på prenumerationsnivå med hjälp + support

Följ instruktionerna nedan för att skapa en supportbegäran med **hjälp + support** i Azure-portalen.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj sedan Hjälp **+ support** på Azure portal-menyn eller sök efter och välj Stöd **+ support**.

    ![Hjälp + Support](./media/networking-quota-request/help-plus-support.png)

1. Välj **Ny supportbegäran**.

    ![Ny supportbegäran](./media/networking-quota-request/new-support-request.png)

1. För **Ärendetyp**väljer du **Tjänst- och prenumerationsgränser (kvoter)**.

    ![Välj prenumerationsgränser från listrutan för ärendetyp](./media/networking-quota-request/select-quota-issue-type.png)

1. Välj den prenumeration som behöver en ökad kvot.

    ![Välj prenumeration nySR](./media/networking-quota-request/select-subscription-support-request.png)

1. Under **Kvottyp**väljer du **Nätverk**. Välj **Nästa: Lösningar**.

    ![Välj kvottyp](./media/networking-quota-request/select-quota-type-network.png)

1. I **PROBLEMINFORMATION**väljer du **Ange information** och fyll i ytterligare information för att behandla din begäran.

    ![Ge information](./media/networking-quota-request/provide-details-link.png)

1. På panelen **Kvotinformation** väljer du en distributionsmodell, en plats och de resurser som ska inkluderas i din begäran.

    ![Kvotinformation DM](./media/networking-quota-request/quota-details-network.png)

1. Ange de nya gränser som du vill använda för prenumerationen. Om du vill ta bort en linje avmarkerar du resursen från **resursmenyn** eller väljer ikonen ignorera "x". När du har angett kvoten för varje resurs väljer du **Spara och fortsätter** att fortsätta med skapandet av supportbegäran.

    ![Nya gränser](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Begära ökning av nätverkskvoten på prenumerationsnivå med hjälp av kvoter för användningar +

Följ dessa instruktioner för att skapa en supportbegäran med hjälp av **Användning + kvot** i Azure-portalen.

1. Från https://portal.azure.comsöker du efter och väljer **Prenumerationer**.

    ![Prenumerationer](./media/networking-quota-request/search-for-suscriptions.png)

1. Välj den prenumeration som behöver en ökad kvot.

    ![Välj en prenumeration](./media/networking-quota-request/select-subscription-change-quota.png)

1. Välj **Användning + kvoter**

    ![Välj användning och kvoter](./media/networking-quota-request/select-usage-plus-quotas.png)

1. I det övre högra hörnet väljer du **Begär ökning**.

    ![Ökning av begäran](./media/networking-quota-request/request-increase-from-subscription.png)

1. Följ stegen som börjar med steg 3 i [Kvotökning av begäran om nätverk på prenumerationsnivå](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Om nätverksbegränsningar

Mer information om nätverksbegränsningar finns i [avsnittet Nätverk i](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) begränsningssidan eller vanliga frågor om nätverksgränser.
