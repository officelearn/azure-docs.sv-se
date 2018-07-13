---
title: Automatisk skala en molntjänst i portalen | Microsoft Docs
description: Lär dig hur du använder portalen för att konfigurera regler för automatisk skalning för en cloud service-webbroll eller worker-roll i Azure.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 29ee71e7946145e50cc875df96b674abec3e12df
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004344"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Så här konfigurerar du automatisk skalning för en molntjänst i portalen

Villkor kan anges för en cloud service-arbetsroll som utlöser en skala in eller ut igen. Villkor för rollen kan baseras på CPU-, disk- eller nätverksbelastningen för rollen. Du kan också ange ett villkor baserat på en meddelandekö eller mått för några andra Azure-resurs som är associerade med prenumerationen.

> [!NOTE]
> Den här artikeln handlar om Cloud Service-web- och worker-roller. När du skapar en virtuell dator (klassisk) direkt finns det i en molntjänst. Du kan skala en standard virtuell dator genom att associera den med en [tillgänglighetsuppsättning](../virtual-machines/windows/classic/configure-availability-classic.md) och aktivera dem manuellt eller inaktivera.

## <a name="considerations"></a>Överväganden
Innan du konfigurerar skalning för ditt program bör du tänka på följande information:

* Skala påverkas av core användning.

    Större rollinstanser använda fler kärnor. Du kan skala ett program bara inom ramen för kärnor för prenumerationen. Anta exempelvis att din prenumeration har en gräns på 20 kärnor. Om du kör ett program med två medelstora cloud services (totalt 4 kärnor), kan du bara skala upp andra molntjänstdistributioner i din prenumeration av återstående 16 kärnor. Mer information om storlekar finns i [Molntjänststorlekar](cloud-services-sizes-specs.md).

* Du kan skala baserat på ett tröskelvärde för kön. Mer information om hur du använder köer finns i [hur du använder Queue Storage-tjänsten](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Du kan även skala andra resurser som är associerade med prenumerationen.

* Om du vill aktivera hög tillgänglighet för ditt program bör du kontrollera att den har distribuerats med två eller fler rollinstanser. Mer information finns i [serviceavtal](https://azure.microsoft.com/support/legal/sla/).

* Autoskala inträffar bara när alla roller finns i **redo** tillstånd.  


## <a name="where-scale-is-located"></a>Där skala finns
Du bör ha molnet bladet för tjänsten visas när du har valt din molntjänst.

1. På bladet för cloud-tjänsten på den **roller och instanser** panelen, väljer du namnet på Molntjänsten.   
   **VIKTIGA**: se till att klicka på molntjänstroll inte rollinstansen som är lägre än rollen.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Välj den **skala** panelen.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatisk skalning
Du kan konfigurera inställningar för en roll med antingen två lägen **manuell** eller **automatisk**. Handboken är som förväntat, du ställer in absolut antal instanser. Automatisk kan men du kan ange regler som styr hur och med hur mycket du ska skalas.

Ange den **skala genom att** alternativet att **schema och Prestandaregler**.

![Inställningar för cloud services med profilen och regel](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. En befintlig profil.
2. Lägg till en regel för den överordnade profilen.
3. Lägg till en annan profil.

Välj **Lägg till profil**. Profilen fastställer vilket läge du vill använda för skalan: **alltid**, **upprepning**, **fast datum**.

När du har konfigurerat profilen och reglerna, Välj den **spara** längst upp.

#### <a name="profile"></a>Profil
Profilen anger minsta och högsta instanser för skalning, och även när det här intervallet för skalan är aktiv.

* **Alltid**

    Alltid ha detta antal instanser som är tillgängliga.  

    ![Molntjänst som alltid skala](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Upprepning**

    Välj en uppsättning veckodagar att skala.

    ![Molnskalning för tjänsten med ett återkommande schema](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Fast datum**

    Ett datumintervall att skala rollen.

    ![Molnskalning för tjänsten med ett fast datum](./media/cloud-services-how-to-scale-portal/select-fixed.png)

När du har konfigurerat profilen, väljer du den **OK** längst ned på profilbladet för.

#### <a name="rule"></a>Regel
Regler har lagts till i en profil och representerar ett villkor som utlöser skalan.

Utlösaren regeln baseras på ett mått av Molntjänsten (CPU-användning, diskaktivitet eller nätverksaktivitet) där du kan lägga till en villkorlig värde. Du kan dessutom ha utlösaren baserat på en meddelandekö eller mått för några andra Azure-resurs som är associerade med prenumerationen.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

När du har konfigurerat regeln, Välj den **OK** längst ned på bladet för regeln.

## <a name="back-to-manual-scale"></a>Tillbaka till manuell skala
Navigera till den [skala inställningar](#where-scale-is-located) och ange den **skala genom att** alternativet att **ett instansantal som anges manuellt**.

![Inställningar för cloud services med profilen och regel](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Den här inställningen tar bort automatiserad skalning från rollen och du kan konfigurera instansantalet direkt.

1. Skala (manuella eller automatiska) alternativet.
2. En roll instans skjutreglaget för att ange instanser att skala till.
3. Instanser av rollen för att skala till.

När du har konfigurerat inställningar för skala, Välj den **spara** längst upp.
