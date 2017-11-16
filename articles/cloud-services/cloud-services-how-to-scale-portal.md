---
title: "Automatisk skala en tjänst i molnet i portal | Microsoft Docs"
description: "Lär dig använda portalen för att konfigurera regler för Automatisk skala för en cloud service-webbroll eller worker-rollen i Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 708b35252429e30b44226030d3d83b928f2a9520
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Så här konfigurerar du automatisk skalning för en tjänst i molnet i portalen

Villkor kan anges för en cloud service-arbetsroll som utlöser en skala in eller ut igen. Villkor för rollen kan baseras på CPU, disk eller nätverksbelastningen för rollen. Du kan också ange ett villkor baserat på en meddelandekö eller mått för vissa andra Azure-resurs som är associerade med prenumerationen.

> [!NOTE]
> Den här artikeln fokuserar på Molntjänsten webb- och arbetsroller roller. När du skapar en virtuell dator (klassisk) direkt finns det i en molntjänst. Du kan skala en vanliga virtuell dator genom att associera den med en [tillgänglighetsuppsättning](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) och aktivera dem manuellt eller inaktivera.

## <a name="considerations"></a>Överväganden
Innan du konfigurerar skalning för ditt program bör du tänka på följande information:

* Skalning påverkas av kärnor användning.

    Större rollinstanser använda flera kärnor. Du kan skala ett program bara inom ramen för kärnor för prenumerationen. Anta till exempel har en begränsning på 20 kärnor för prenumerationen. Om du kör ett program med två medelstora molntjänster (totalt 4 kärnor) kan du bara skala upp andra distributioner av molntjänster i din prenumeration av återstående 16 kärnor. Mer information om storlekar finns [Molntjänststorlekar](cloud-services-sizes-specs.md).

* Du kan skala baserat på ett tröskelvärde för meddelandet. Mer information om hur du använder köer finns [hur du använder tjänsten Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Du kan även skala andra resurser som är associerade med prenumerationen.

* Om du vill aktivera hög tillgänglighet för ditt program bör du kontrollera att den har distribuerats med två eller flera rollinstanser. Mer information finns i [serviceavtal](https://azure.microsoft.com/support/legal/sla/).


## <a name="where-scale-is-located"></a>Där skala finns
Du bör ha cloud service bladet som är synliga när du har valt Molntjänsten.

1. I bladet cloud service på den **roller och instanser** panelen, Välj namnet på Molntjänsten.   
   **VIKTIGA**: se till att klicka på rolltjänsten moln inte instansen som är under rollen.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Välj den **skala** panelen.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatisk skalning
Du kan konfigurera inställningar för en roll med antingen två lägen **manuell** eller **automatisk**. Manuell är som förväntat, du kan ange absolut antal instanser. Automatisk dock möjligt för dig att ange regler som styr hur och med hur mycket du ska skalas.

Ange den **skala genom** att **schema och prestanda regler**.

![Cloud services skalinställningar med profil och regel](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. En befintlig profil.
2. Lägga till en regel för den överordnade profilen.
3. Lägg till en annan profil.

Välj **lägga till profilen**. Profilen avgör vilket läge du vill använda för skalan: **alltid**, **återkommande**, **fast datum**.

När du har konfigurerat de regler och profil, väljer du den **spara** längst upp.

#### <a name="profile"></a>Profil
Profilen anger lägsta och högsta förekomster för skala, och även när det här intervallet för skalan är aktiv.

* **Alltid**

    Alltid ha detta antal instanser som är tillgängliga.  

    ![Molntjänst som alltid skala](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Upprepning**

    Välj en uppsättning veckodagar att skala.

    ![Tjänsten molnskala med ett återkommande schema](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Fast datum**

    Ett datumintervall att skala rollen.

    ![Tjänsten molnskala med ett fast datum](./media/cloud-services-how-to-scale-portal/select-fixed.png)

När du har konfigurerat profilen, Välj den **OK** längst ned på profilbladet för.

#### <a name="rule"></a>Regel
Regler har lagts till i en profil och representerar ett villkor som utlöser skalan.

Utlösaren regeln baseras på ett mått av Molntjänsten (CPU-användning, diskaktivitet eller nätverksaktivitet) som du kan lägga till en villkorlig värde. Du kan dessutom ha utlösaren baserat på en meddelandekö eller mått för vissa andra Azure-resurs som är associerade med prenumerationen.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

När du har konfigurerat regeln, väljer du den **OK** längst ned på bladet för regeln.

## <a name="back-to-manual-scale"></a>Tillbaka till manuell skala
Navigera till den [skala inställningar](#where-scale-is-located) och ange den **skala genom** att **instansantal som anges manuellt**.

![Cloud services skalinställningar med profil och regel](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Den här inställningen tar bort automatisk skalning från rollen och du kan ange instansantal direkt.

1. Skala (manuell eller automatisk) alternativet.
2. En roll instans skjutreglaget för att ange de instanser som du vill skala till.
3. Instanser av rollen att skala till.

När du har konfigurerat inställningarna för skala, Välj den **spara** längst upp.
