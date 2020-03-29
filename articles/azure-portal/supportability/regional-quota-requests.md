---
title: Begär en ökning av Azures regionala vCPU-kvotgränser
description: Så här begär du en ökning av kvotgränsen för vCPU för en region i Azure-portalen.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843692"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standardkvot: Öka gränserna per region

Azure Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer:

* Virtuella datorer med användningsbaserad betalning och *reserverade VM-instanser* omfattas av en *vanlig vCPU-kvot*. *Pay-as-you-go VMs*
* *Spot virtuella datorer* omfattas av en *plats vCPU kvot*.

Standard vCPU-kvoten för delningsbaserade och reserverade instanser för virtuella datorer tillämpas på två nivåer för varje prenumeration i varje region:

* Den första nivån är den *totala regionala vCPUs-gränsen*, i alla VM-serier.
* Den andra nivån är *vCPUs-gränsen per VM-serien,* till exempel virtuella processorer i D-serien.

När du distribuerar en ny plats-VM får den totala nya och befintliga vCPU-användningen för den VM-serien inte överstiga den godkända vCPU-kvoten för just den VM-serien. Dessutom bör det totala antalet nya och befintliga virtuella processorer som distribueras över alla VM-serier inte överstiga den totala godkända regionala vCPU-kvoten för prenumerationen. Om någon av dessa kvoter överskrids tillåts inte vm-distributionen.

Du kan begära en ökning av vCPU-kvotgränsen för VM-serien med hjälp av Azure-portalen. En ökning av vm-seriens kvot ökar automatiskt den totala regionala vCPU-gränsen med samma belopp.

När du skapar en ny prenumeration kanske det totala standardantalet regionala virtuella processorer inte är lika med den totala virtuella processorkvoten för alla enskilda virtuella datorer för alla enskilda virtuella datorer. Den här avvikelsen kan resultera i en prenumeration med tillräckligt med kvot för varje enskild VM-serie som du vill distribuera. Men det kanske inte finns tillräckligt med kvot för att rymma den totala regionala virtuella processorer för alla distributioner. I det här fallet måste du skicka en begäran om att uttryckligen öka gränsen för det totala antalet regionala virtuella processorer. Den totala regionala vCPU-gränsen kan inte överskrida den totala godkända kvoten för alla VM-serier för regionen.

Mer information om vanliga vCPU-kvoter finns i [virtuella dator vCPU-kvoter](../../virtual-machines/windows/quotas.md) och [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Mer information om hur du ökar vCPU-begränsningar för plats för virtuella datorer finns i [Platskvot: Öka gränserna för alla VM-serier](low-priority-quota.md).

Du kan begära en ökning av din vCPU-standardkvotgräns per region på något av två sätt.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Begär en kvotökning per region från hjälp + support

Så här begär du en ökning av vCPU-kvoten per region från **hjälp + support:**

1. På [Portalmenyn i Azure](https://portal.azure.com) väljer du **Stöd för Hjälp +**.

   ![Länken "Hjälp + support"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Välj **Ny supportbegäran i** **Hjälp + support**.

    ![Ny supportbegäran](./media/resource-manager-core-quotas-request/new-support-request.png)

1. För **Ärendetyp**väljer du **Tjänst- och prenumerationsgränser (kvoter)**.

   ![Välj en ärendetyp](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. För **Prenumeration**väljer du den prenumeration vars kvot du vill öka.

   ![Välj en prenumeration](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. För **typen Kvot**väljer du **Andra begäranden**.

   ![Välj en kvottyp](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Välj **Nästa: Lösningar** för att öppna **PROBLEMINFORMATION**. I **Beskrivning**anger du följande information:

    1. För **Distributionsmodell**anger du **Resurshanteraren**.  
    1. För **Region**anger du önskad region, till exempel **Östra US 2.**  
    1. För **Ny gräns**anger du en ny vCPU-gräns för regionen. Det här värdet bör inte överstiga summan av de godkända kvoterna för enskilda SKU-serier för den här prenumerationen.

    ![Ange information om kvotbegäran](./media/resource-manager-core-quotas-request/regional-details.png)

1. Välj **Granska + skapa** om du vill fortsätta att skapa supportbegäran.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Begär en kvotökning per region från prenumerationer

Så här begär du en ökning av vCPU-kvoten per region från **Prenumerationer:**

1. Sök efter och välj **Prenumerationer**i [Azure-portalen](https://portal.azure.com).

   ![Gå till Prenumerationer i Azure-portalen](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Välj den prenumeration vars kvot du vill öka.

   ![Välj en prenumeration som ska ändras](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Välj Användning + **kvoter**i den vänstra rutan .

   ![Länken Följ användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Längst upp till höger väljer du **Begär ökning**.

   ![Välj om du vill öka kvoten](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Välj **Andra begäranden i** **Kvottypen**.

   ![Välj kvottyp](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Välj **Nästa: Lösningar** för att öppna **PROBLEMINFORMATION**. Ange följande ytterligare information i rutan **Beskrivning:**

    1. För **Distributionsmodell**anger du **Resurshanteraren**.  
    1. För **Region**anger du önskad region, till exempel **Östra US 2.**  
    1. För **Ny gräns**anger du en ny vCPU-gräns för regionen. Det här värdet bör inte överstiga summan av de godkända kvoterna för enskilda SKU-serier för den här prenumerationen.

    ![Ange information i information](./media/resource-manager-core-quotas-request/regional-details.png)

1. Välj **Granska + skapa** om du vill fortsätta att skapa supportbegäran.
