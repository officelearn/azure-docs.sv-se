---
title: Spot kvot för virtuella datorer - Azure
description: Öka kvotgränserna för spot-datorer, som tillhandahåller en modell för Azure-användning som gör att du kan ta lägre kostnader i utbyte mot att låta Azure ta bort virtuella datorer efter behov.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842820"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Platskvot: Öka gränserna för alla VM-serier

Spot virtuella datorer (VMs) ger en annan modell av Azure-användning. De låter dig ta lägre kostnader i utbyte mot att låta Azure ta bort virtuella datorer efter behov för användningsbaserad betalning eller reserverade VM-instansdistributioner. Mer information om spot-datorer finns i [virtuella azure spot-datorer för skalningsuppsättningar för virtuella datorer](../../virtual-machine-scale-sets/use-spot.md).

Azure Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer:

* Virtuella datorer med användningsbaserad betalning och *reserverade VM-instanser* omfattas av en *vanlig vCPU-kvot*. *Pay-as-you-go VMs*
* *Spot virtuella datorer* omfattas av en *plats vCPU kvot*.

För kvottypen för plats vCPU tillämpas resurshanterarens vCPU-kvoter över alla tillgängliga serier för virtuella datorer som en enda regional gräns.

När du distribuerar en ny plats-VM får den totala nya och befintliga vCPU-användningen för alla spot-VM-instanser inte överskrida den godkända punkt-vCPU-kvotgränsen. Om spotkvoten överskrids tillåts inte platsen för vm-distribution.

I den här artikeln beskrivs hur du begär en ökning av spotgränsen för vCPU-kvoten med hjälp av Azure-portalen.

Mer information om vanliga vCPU-kvoter finns i [virtuella dator vCPU-kvoter](../../virtual-machines/windows/quotas.md) och [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Mer information om hur du ökar vCPU-gränsen per region finns i [Standardkvot: Öka gränserna per region](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Begär en ökning av kvotgränsen från hjälp + support

Så här begär du en ökning av kvotgränsen för alla serier för virtuella datorer med **hjälp + stöd:**

> [!NOTE]
> Du kan också begära en ökning av kvotgränsen för flera regioner via ett enda supportärende. Mer information finns i steg 8.

1. På [Portalmenyn i Azure](https://portal.azure.com) väljer du **Stöd för Hjälp +**.

   ![Supportlänken hjälp +](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Välj **Ny supportbegäran i** **Hjälp + support**.

    ![Skapa en ny supportbegäran](./media/resource-manager-core-quotas-request/new-support-request.png)

1. För **Ärendetyp**väljer du **Tjänst- och prenumerationsgränser (kvoter)**.

   ![Välj en ärendetyp](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. För **Prenumeration**väljer du den prenumeration vars kvot du vill öka.

   ![Välj en prenumeration för en utökad kvot](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. För **Kvottyp**väljer du **Prenumerationsgränsen för Compute-VM (cores-vCPUs).**

   ![Välj en kvottyp](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Välj **Nästa: Lösningar** för att öppna **PROBLEMINFORMATION**. Välj **Ange information om** du vill ange ytterligare information.

   ![Länken "Ge information"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Gör följande i **Kvotinformation:**

   1. För **distributionsmodell**väljer du lämplig modell och väljer plats för **Platser.**

      ![Ange ytterligare kvotinformation](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. För den valda platsen under Typer väljer du **Spot** **under** **Typer**.

      ![Välj punkttyp](./media/resource-manager-core-quotas-request/select-spot-type.png)

       Under **Typer**kan du begära både standard- och spotkvottyper från ett enda supportärende via stöd för flera val.

       Mer information finns i [Standardkvot: Öka gränserna för VM-serien](per-vm-quota-requests.md).

   1. Ange den nya kvotgräns som du vill använda för den här prenumerationen.

      ![Välj en ny kvot för spot-VM](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Om du vill begära en kvotökning för mer än en plats väljer du ytterligare en plats i **Platser**och väljer sedan en lämplig vm-typ. Du kan sedan ange en gräns som gäller för den extra platsen.

   ![Ange ytterligare platser i kvotinformation](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Välj **Spara och fortsätt** att skapa supportbegäran.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Begär en ökning av kvotgränsen från fönstret Prenumerationer

Så här begär du en ökning av punktkvotgränsen för alla VM-serier från fönstret **Prenumerationer:**

> [!NOTE]
> Du kan också begära en ökning av kvotgränsen för flera regioner via ett enda supportärende. Mer information finns i steg 7.

1. Sök efter och välj **Prenumerationer**i [Azure-portalen](https://portal.azure.com).

   ![Prenumerationer i Azure portal-sökning](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Välj den prenumeration vars kvot du vill öka.

   ![Prenumerationer att välja för ändringar](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Välj Användning + **kvoter**i den vänstra rutan .

   ![Länken "Användning + kvoter"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Längst upp till höger väljer du **Begär ökning**.

   ![Välj om du vill öka kvoten](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. För **Kvottyp**väljer du **Prenumerationsgränsen för Compute-VM (cores-vCPUs).**

   ![Välj en kvottyp](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Välj **Nästa: Lösningar** för att öppna **PROBLEMINFORMATION**. Välj **Ange information om** du vill ange ytterligare information. I **Kvotinformation**anger du följande information:

   1. För **distributionsmodell**väljer du lämplig modell och väljer plats för **Platser.**

      ![Ange kvotinformation](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. För den valda platsen under Typer väljer du **Spot** **under** **Typer**.

      ![Välj punkttyp](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Mer information finns i [Standardkvot: Öka gränserna för VM-serien](per-vm-quota-requests.md).

   1. Ange den nya kvotgräns som du vill använda för den här prenumerationen.

      ![Ange ett nytt värde för vCPU-gräns](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Om du vill begära en kvotökning för mer än en plats väljer du ytterligare en plats i **Platser**och väljer sedan en lämplig vm-typ. Du kan sedan ange en gräns som gäller för den extra platsen.

   ![Välj ytterligare platser i kvotinformation](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Välj **Spara och fortsätt** att skapa supportbegäran.
