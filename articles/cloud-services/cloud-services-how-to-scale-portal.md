---
title: Skala en molntjänst automatiskt i portalen | Microsoft-dokument
description: Lär dig hur du använder portalen för att konfigurera regler för automatisk skalning för en webbroll för molntjänsten eller arbetsrollen i Azure.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 5880544137855a2ea5bcd6d6e4bada46563564ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360845"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Konfigurera automatisk skalning för en molntjänst i portalen

Villkor kan ställas in för en molntjänstarbetstjänstroll som utlöser en skala in eller ut-åtgärd. Villkoren för rollen kan baseras på rollens CPU, disk eller nätverksbelastning. Du kan också ange ett villkor baserat på en meddelandekö eller måttet för någon annan Azure-resurs som är associerad med din prenumeration.

> [!NOTE]
> Den här artikeln fokuserar på Cloud Service-webb- och arbetarroller. När du skapar en virtuell dator (klassisk) direkt finns den i en molntjänst. Du kan skala en virtuell standarddator genom att associera den med en [tillgänglighetsuppsättning](../virtual-machines/windows/classic/configure-availability-classic.md) och manuellt aktivera eller inaktivera dem.

## <a name="considerations"></a>Överväganden
Du bör tänka på följande information innan du konfigurerar skalning för ditt program:

* Skalning påverkas av kärnanvändningen.

    Större rollinstanser använder fler kärnor. Du kan skala ett program endast inom gränsen för kärnor för din prenumeration. Anta till exempel att din prenumeration har en gräns på 20 kärnor. Om du kör ett program med två medelstora molntjänster (totalt 4 kärnor) kan du bara skala upp andra molntjänstdistributioner i din prenumeration med de återstående 16 kärnorna. Mer information om storlekar finns i [Molntjänststorlekar](cloud-services-sizes-specs.md).

* Du kan skala baserat på ett tröskelvärde för kömeddelande. Mer information om hur du använder köer finns i [Så här använder du tjänsten Kölagring](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Du kan också skala andra resurser som är associerade med din prenumeration.

* Om du vill aktivera hög tillgänglighet för ditt program bör du se till att det distribueras med två eller flera rollinstanser. Mer information finns i [Servicenivåavtal](https://azure.microsoft.com/support/legal/sla/).

* Automatisk skalning sker bara när alla roller är i **redoläge.**  


## <a name="where-scale-is-located"></a>Var skalan finns
När du har valt din molntjänst bör du visa molntjänstbladet.

1. På molntjänstbladet, på panelen **Roller och instanser,** väljer du namnet på molntjänsten.   
   **VIKTIGT:** Se till att klicka på molntjänstrollen, inte rollinstansen som finns under rollen.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Markera **skalpanelen.**

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatisk skala
Du kan konfigurera skalningsinställningar för en roll med antingen två **lägen manuell** eller **automatisk**. Manuell är som du förväntar dig, du ställer in det absoluta antalet instanser. Automatisk kan du dock ställa in regler som styr hur och med hur mycket du ska skala.

Ange alternativet **Skala efter** för schema **och prestandaregler**.

![Molntjänster skala inställningar med profil och regel](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. En befintlig profil.
2. Lägg till en regel för den överordnade profilen.
3. Lägg till en annan profil.

Välj **Lägg till profil**. Profilen avgör vilket läge du vill använda för skalan: **alltid**, **återkommande,** **fast datum**.

När du har konfigurerat profilen och reglerna väljer du **ikonen Spara** högst upp.

#### <a name="profile"></a>Profil
Profilen anger lägsta och högsta instanser för skalan och även när det här skalområdet är aktivt.

* **Alltid**

    Håll alltid det här intervallet av instanser tillgängliga.  

    ![Molntjänst som alltid skalas](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Återkommande**

    Välj en uppsättning veckodagar som ska skalas.

    ![Skala molntjänsten med ett återkommande schema](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Fast datum**

    Ett fast datumintervall för att skala rollen.

    ![CLoud-serviceskala med ett fast datum](./media/cloud-services-how-to-scale-portal/select-fixed.png)

När du har konfigurerat profilen väljer du **OK-knappen** längst ned på profilbladet.

#### <a name="rule"></a>Regel
Regler läggs till i en profil och representerar ett villkor som utlöser skalan.

Regelutlösaren baseras på ett mått på molntjänsten (CPU-användning, diskaktivitet eller nätverksaktivitet) som du kan lägga till ett villkorsvärde till. Dessutom kan du ha utlösaren baserat på en meddelandekö eller måttet för någon annan Azure-resurs som är associerad med din prenumeration.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

När du har konfigurerat regeln väljer du **OK-knappen** längst ned i regelbladet.

## <a name="back-to-manual-scale"></a>Tillbaka till manuell skala
Navigera till [skalningsinställningarna](#where-scale-is-located) och ange alternativet **Skala efter** till ett **instansantal som jag anger manuellt**.

![Molntjänster skala inställningar med profil och regel](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Den här inställningen tar bort automatisk skalning från rollen och sedan kan du ställa in instansantalet direkt.

1. Alternativet skala (manuell eller automatiserad).
2. Ett rollinstansreglage som instanserna ska skalas till.
3. Instanser av rollen att skala till.

När du har konfigurerat skalningsinställningarna väljer du **ikonen Spara** högst upp.



