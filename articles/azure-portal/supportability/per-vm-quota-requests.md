---
title: Begär en ökning av vCPU-kvotgränser per Azure VM-serie
description: Så här begär du en ökning av kvotgränsen för vCPU för en VM-serie i Azure-portalen, vilket ökar den totala regionala vCPU-gränsen med samma belopp.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843769"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standardkvot: Öka gränserna för VM-serien

Azure Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer:

* Virtuella datorer med användningsbaserad betalning och *reserverade VM-instanser* omfattas av en *vanlig vCPU-kvot*. *Pay-as-you-go VMs*
* *Spot virtuella datorer* omfattas av en *plats vCPU kvot*.

Standard vCPU-kvoten för delningsbaserade och reserverade instanser för virtuella datorer tillämpas på två nivåer för varje prenumeration i varje region:

* Den första nivån är den *totala regionala vCPUs-gränsen*, i alla VM-serier.
* Den andra nivån är *vCPUs-gränsen per VM-serien,* till exempel vCUs i Dv3-serien.

När du distribuerar en ny plats-VM får den totala nya och befintliga vCPU-användningen för den VM-serien inte överstiga den godkända vCPU-kvoten för just den VM-serien. Dessutom bör det totala antalet nya och befintliga virtuella processorer som distribueras över alla VM-serier inte överstiga den totala godkända regionala vCPU-kvoten för prenumerationen. Om någon av dessa kvoter överskrids tillåts inte vm-distributionen.

Du kan begära en ökning av vCPU-kvotgränsen för VM-serien med hjälp av Azure-portalen. En ökning av vm-seriens kvot ökar automatiskt den totala regionala vCPU-gränsen med samma belopp.

Mer information om vanliga vCPU-kvoter finns i [virtuella dator-vCPU-kvoter](../../virtual-machines/windows/quotas.md) och [Azure-prenumerations- och tjänstgränser](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests).

Mer information om hur du ökar vCPU-gränsen per region för standardkvot finns i [Standardkvot: Öka gränserna per region](regional-quota-requests.md).

Mer information om hur du ökar vCPU-begränsningar för plats för virtuella datorer finns i [Platskvot: Öka gränserna för alla VM-serier](low-priority-quota.md).

Du kan begära en ökning av vanliga vCPU-kvotgränser per VM-serie på något av två sätt, enligt beskrivningen i följande avsnitt.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Begär en standardkvotökning från hjälp + support

Så här begär du en standardökning av vCPU-kvoter per VM-serie från **hjälp + support:**

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

1. Gör följande i **Kvotinformationen:**

   ![Ytterligare kvotinformation för TProvide](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. För **distributionsmodell**väljer du lämplig modell.

   1. För **Platser**väljer du en plats. För den valda platsen under Typer väljer du **Standard** **under** **Typer**.

      ![Kvotdetaljer - kvottyper](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Under **Typer**kan du begära både standard- och spotkvottyper från ett enda supportärende via stöd för flera val.

      Mer information om hur du ökar spotkvotgränserna finns i [Virtuella Azure-plats-datorer för skaluppsättningar för virtuella datorer](../../virtual-machine-scale-sets/use-spot.md).

   1. Välj SKU-serien för utökade kvoter i **Standard.**

      ![Kvotdetaljer - SKU-serien](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Ange de nya kvotgränser som du vill använda för den här prenumerationen. Om du vill ta bort en SKU från listan avmarkerar du kryssrutan bredvid SKU:n eller markerar ikonen "Ignorera"X.

      ![Välj en ny vCPU-gräns](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Om du vill begära en kvotökning för mer än en plats väljer du ytterligare en plats i **Platser**och väljer sedan en lämplig vm-typ. Du kan sedan ange en gräns som gäller för den extra platsen.

   ![Ange ytterligare platser i kvotinformation](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Välj **Spara och fortsätt** att skapa supportbegäran.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Begära en standardkvotökning från prenumerationer

Så här begär du en standardökning av vCPU-kvoter per VM-serie från **Prenumerationer:**

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

1. Gör följande i **Kvotinformationen:**

   1. För **distributionsmodell**väljer du lämplig modell och väljer plats för **Platser.**

      ![Ange kvotinformation](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. För den valda platsen väljer du **Välj en typ**under **Typer**och väljer sedan **Standard**.

      ![Välj standardtyp](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Under **Typer**kan du begära både standard- och spotkvottyper från ett enda supportärende via stöd för flera val.

      Mer information om hur du ökar spotkvotgränserna finns i [Virtuella Azure-plats-datorer för skaluppsättningar för virtuella datorer](../../virtual-machine-scale-sets/use-spot.md).

   1. För **Standard**väljer du den SKU-serie vars kvoter du vill öka.

      ![Kvotdetaljer - SKU-serien](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Ange de nya kvotgränser som du vill använda för den här prenumerationen. Om du vill ta bort en SKU från listan avmarkerar du kryssrutan bredvid SKU:n eller markerar ikonen "Ignorera"X.

      ![Välj en ny vCPU-gräns](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Om du vill begära en kvotökning för mer än en plats väljer du ytterligare en plats i **Platser**och väljer sedan en lämplig vm-typ.

   Det här steget förinläsningar av SKU-serien som du har valt för tidigare platser. Ange de kvotgränser som du vill tillämpa på ytterligare serier.

   ![Välj ytterligare platser i kvotinformation](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Välj **Spara och fortsätt** att skapa supportbegäran.
