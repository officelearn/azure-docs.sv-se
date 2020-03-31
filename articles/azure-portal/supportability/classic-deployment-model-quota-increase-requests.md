---
title: Klassisk Azure-distributionsmodell
description: Den klassiska distributionsmodellen, som nu ersätts av Resource Manager-modellen, tillämpar en global vCPU-kvotgräns för virtuella datorer och skaluppsättningar för virtuella datorer.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835631"
---
# <a name="classic-deployment-model"></a>Klassisk distributionsmodell

Den klassiska distributionsmodellen är den äldre generationens Azure-distributionsmodell. Den tillämpar en global vCPU-kvotgräns för virtuella datorer och skaluppsättningar för virtuella datorer och virtuella datorer. Den klassiska distributionsmodellen rekommenderas inte längre och ersätts nu av Resource Manager-modellen.

Mer information om dessa två distributionsmodeller och fördelarna med att använda Resource Manager finns i [Resource Manager och klassisk distribution](../../azure-resource-manager/management/deployment-models.md).

När en ny prenumeration skapas tilldelas en standardkvot för virtuella processorer till den. När som helst en ny virtuell dator ska distribueras med hjälp av den klassiska distributionsmodellen, får summan av ny och befintlig vCPU-användning i alla regioner inte överstiga den vCPU-kvot som godkänts för den klassiska distributionsmodellen.

Mer information om kvoter finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Du kan begära en ökning av vCPU-kvotgränsen för den klassiska distributionsmodellen. Använd antingen **hjälp + support** eller användning + **kvoter** i Azure-portalen.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Begär vCPU-kvotökning per VM-serie på prenumerationsnivå med hjälp + support

Följ instruktionerna nedan för att skapa en supportbegäran med **hjälp + support** i Azure-portalen.

1. På [Portalmenyn i Azure](https://portal.azure.com) väljer du **Stöd för Hjälp +**.

   ![Välj hjälp + support i Azure-portalen](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Välj **Ny supportbegäran**.

   ![Skapa en ny supportbegäran i Azure-portalen](./media/resource-manager-core-quotas-request/new-support-request.png)

1. I **ärendetyp**väljer du **Service- och prenumerationsgränser (kvoter)**.

   ![Välj kvoter som typ av problem](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Välj den prenumeration vars kvot du vill öka.

   ![Välj prenumeration som en kvot ska öka](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. För **Kvottyp**väljer du **Beräkning -VM (cores-vCPUs) prenumerationsgräns ökar**.

   ![Välj kvottyp som ska öka](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Välj **Nästa: Lösningar** för att öppna **PROBLEMINFORMATION**. Välj **Ange information om** du vill ange ytterligare information.

   ![Ge information som hjälper din förfrågan tillsammans](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Välj **Klassisk** **i Kvotinformation**och välj en **plats**.

   ![Lägga till information, inklusive distributionsmodell och plats](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. För **SKU-familjen**väljer du en eller flera SKU-familjer som ska ökas.

   ![Ange att SKU-familjen ska öka](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Ange de nya gränser som du vill använda för prenumerationen. Om du vill ta bort en linje avmarkerar du SKU från **SKU-familjen** eller väljer ikonen "Ignorera"X.To remove a line, unselect the SKU from SKU family or select the discard "X" icon. När du har angett en kvot för varje SKU-familj väljer du **Spara och fortsätt** i **kvotinformation** för att fortsätta med supportbegäran.

   ![Begär nya gränser](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Begär vCPU-kvotökning per VM-serie på prenumerationsnivå med hjälp av Användning + kvoter

Följ instruktionerna nedan för att skapa en supportbegäran med hjälp av **Användning + kvoter** i Azure-portalen.

1. Sök efter och välj **Prenumerationer**i [Azure-portalen](https://portal.azure.com).

   ![Gå till Prenumerationer i Azure-portalen](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Välj den prenumeration vars kvot du vill öka.

   ![Välj prenumeration att ändra](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Välj **Användning + kvoter**.

   ![Välj användning och kvoter för en prenumeration](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. I det övre högra hörnet väljer du **Begär ökning**.

   ![Välj om du vill öka kvoten](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Välj **Prenumerationsgränsen för Beräkning-VM (cores-vCPUs) ökar** när **kvottypen**.

   ![Välj kvottyp](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Välj **Nästa: Lösningar** för att öppna **PROBLEMINFORMATION**. Välj **Ange information om** du vill ange ytterligare information.

   ![Ange information för din förfrågan](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Välj **Klassisk** och en **plats** **i Kvotinformation**.

   ![Välj kvotinformation inklusive distributionsmodell och plats](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Välj en eller flera SKU-familjer för en ökning.

   ![Välj SKU-familj för ökning](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Ange de nya gränser som du vill använda för prenumerationen. Om du vill ta bort en linje avmarkerar du SKU från **SKU-familjen** eller väljer ikonen "Ignorera"X.To remove a line, unselect the SKU from SKU family or select the discard "X" icon. När du har angett en kvot för varje SKU-familj väljer du **Spara och fortsätt** i **kvotinformation** för att fortsätta med supportbegäran.

   ![Ange ny kvot](./media/resource-manager-core-quotas-request/new-limits-classic.png)

