---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748900"
---
Den här artikeln ger dig en översikt över tillgänglighetsfunktionerna för virtuella Azure-datorer (VMs).

## <a name="high-availability"></a>Hög tillgänglighet

Arbetsbelastningar är vanligtvis spridda över olika virtuella datorer för att få hög dataflöde, prestanda och för att skapa redundans om en virtuell dator påverkas på grund av en uppdatering eller annan händelse. 

Det finns få alternativ som Azure tillhandahåller för att uppnå hög tillgänglighet. Låt oss först tala om grundläggande konstruktioner. 

### <a name="availability-zones"></a>Tillgänglighetszoner

[Tillgänglighetszoner](../articles/availability-zones/az-overview.md) utökar den kontrollnivå du har för att upprätthålla tillgängligheten för program och data på dina virtuella datorer. En tillgänglighetszon är en fysiskt separat zon, inom en Azure-region. Det finns tre tillgänglighetszoner per Azure-region som stöds. 

Varje tillgänglighetszon har en distinkt strömkälla, nätverk och kylning. Genom att skapa dina lösningar för att använda replikerade virtuella datorer i zoner kan du skydda dina appar och data från förlusten av ett datacenter. Om en zon komprometterats är replikerade appar och data omedelbart tillgängliga i en annan zon. 

![Tillgänglighetszoner](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Läs mer om hur du distribuerar en [Virtuell Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) eller [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) i en tillgänglighetszon.


### <a name="fault-domains"></a>Feldomäner

En feldomän är en logisk grupp av underliggande maskinvara som delar en gemensam strömkälla och nätverksswitch, ungefär som ett rack i ett lokalt datacenter. 

### <a name="update-domains"></a>Uppdateringsdomäner

En uppdateringsdomän är en logisk grupp av underliggande maskinvara som kan underhållas eller startas om samtidigt. 

På så sätt säkerställs att minst en instans av ditt program alltid körs vid ett periodiskt underhåll av Azure-plattformen. Ordningen på uppdateringsdomäner som startas om kanske inte fortsätter sekventiellt under underhåll, men endast en uppdateringsdomän startas om åt gången.


## <a name="virtual-machines-scale-sets"></a>Skaluppsättningar för virtuella datorer 

Med Azure-skalningsuppsättningar för virtuella datorer kan du skapa och hantera en grupp belastningsbalanserade virtuella datorer. Antal VM-instanser kan automatiskt öka eller minska som svar på efterfrågan eller ett definierat schema. Skalningsuppsättningar ger hög tillgänglighet till dina program och gör att du kan hantera, konfigurera och uppdatera många virtuella datorer centralt. Vi rekommenderar att två eller flera virtuella datorer skapas inom en skalningsuppsättning för att tillhandahålla ett program med högtillgänglighet och för att uppfylla [99,95 % Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Det finns ingen kostnad för själva skalningsuppsättningen, du betalar bara för varje VM-instans som du skapar. När en enda virtuell dator använder [Azure premium SSD-enheter](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)gäller Azure SLA för oplanerade underhållshändelser. Virtuella datorer i en skalningsuppsättning kan distribueras över flera uppdateringsdomäner och feldomäner för att maximera tillgängligheten och motståndskraften mot avbrott på grund av avbrott i datacenter och planerade eller oplanerade underhållshändelser. Virtuella datorer i en skalningsuppsättning kan också distribueras till en enda tillgänglighetszon eller regionalt. Distributionsalternativ för tillgänglighetszon kan variera beroende på orchestration-läget.

### <a name="preview-orchestration-mode-preview"></a>Förhandsgranskning: Förhandsversion av orchestration-läge
Med skalningsuppsättningar för virtuella datorer kan du ange orchestration-läge.  Med den virtuella datorn skala uppsättning orkestrering läge (förhandsvisning), kan du nu välja om skalningsuppsättningen ska orkestrera virtuella datorer som skapas uttryckligen utanför en skalningsuppsättning konfigurationsmodell, eller virtuella datorinstanser skapas implicit baserat på konfigurationsmodellen. Välj det orchestration-läge som vm-orkestreringsmodellen gör att du kan gruppera explicit definierade virtuella datorer tillsammans i en region eller i en tillgänglighetszon. Virtuella datorer som distribueras i en tillgänglighetszon ger zonisolering till virtuella datorer är de bundna till tillgänglighetszongränsen och inte utsätts för några fel som kan uppstå i andra tillgänglighetszon i regionen. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Konfigurationsmodell för virtuell dator| Inga. VirtualMachineProfile är odefinierad i skalningsuppsättningsmodellen. | Krävs. VirtualMachineProfile fylls i skalningsuppsättningsmodellen. |
| Lägga till ny virtuell dator i skalningsuppsättning| Virtuella datorer läggs uttryckligen till i skalningsuppsättningen när den virtuella datorn skapas. | Virtuella datorer skapas implicit och läggs till i skalningsuppsättningen baserat på vm-konfigurationsmodellen, instansantalet och regler för automatisk skalning. |
| Tillgänglighetszoner| Stöder regional distribution eller virtuella datorer i en tillgänglighetszon| Stöder regional distribution eller flera tillgänglighetszoner. Kan definiera zonbalanseringsstrategin |
| Feldomäner| Kan definiera antalet feldomäner. 2 eller 3 baserat på regionalt stöd och 5 för tillgänglighetszon. Den tilldelade vm-feldomänen kommer att finnas kvar med vm-livscykeln, inklusive deallocate och restart. | Kan definiera 1, 2 eller 3 feldomäner för distributioner som inte är zondistributioner och 5 för distributioner av tillgänglighetszoner. Den tilldelade VM-feldomänen kvarstår inte med vm-livscykeln, virtuella datorer tilldelas en feldomän vid tidpunkten för allokeringen. |
| Uppdateringsdomäner| Ej tillämpligt. Uppdateringsdomäner mappas automatiskt till feldomäner| Ej tillämpligt. Uppdateringsdomäner mappas automatiskt till feldomäner |

**Feldomäner och uppdateringsdomäner**

Skala uppsättningar för virtuella datorer förenklar utformningen för hög tillgänglighet genom att justera feldomäner och uppdatera domäner. Du behöver bara definiera antalet feldomäner för skalningsuppsättningen. Antalet feldomäner som är tillgängliga för skalningsuppsättningarna kan variera mellan olika regioner. Se [Hantera tillgängligheten för virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).


## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
En tillgänglighetsuppsättning är en logisk gruppering av virtuella datorer i ett datacenter som gör att Azure kan förstå hur ditt program är byggt för att tillhandahålla redundans och tillgänglighet. Vi rekommenderar att två eller flera virtuella datorer skapas inom en tillgänglighetsuppsättning för att tillhandahålla ett program med hög tillgänglighet och för att uppfylla [99,95 % Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Det finns ingen kostnad för själva tillgänglighetsuppsättningen, du betalar bara för varje VM-instans som du skapar. När en enda virtuell dator använder [Azure premium SSD-enheter](../articles/virtual-machines/windows/disks-types.md#premium-ssd)gäller Azure SLA för oplanerade underhållshändelser.

I en tillgänglighetsuppsättning distribueras virtuella datorer automatiskt över dessa feldomäner. På så sätt begränsas påverkan av potentiella fel på fysisk maskinvara, nätverksavbrott och strömavbrott.

För virtuella datorer som använder [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) justeras de virtuella datorerna efter feldomänerna för hanterade diskar när en hanterad tillgänglighetsuppsättning används. På så sätt säkerställs att alla hanterade diskar som är kopplade till en virtuell dator finns i samma feldomän. 

Endast virtuella datorer med hanterade diskar kan skapas i en hanterad tillgänglighetsuppsättning. Antalet feldomäner kan vara två eller tre, beroende på region. Du kan läsa mer om dessa hanterade diskfelsdomäner för [virtuella Linux-datorer](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) eller [Virtuella Windows-datorer](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Uppsättning för hanterad tillgänglighet](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Virtuella datorer inom en tillgänglighetsuppsättning distribueras också automatiskt över uppdateringsdomäner. 

![Tillgänglighetsuppsättningar](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Nästa steg
Nu kan du börja använda dessa tillgänglighets- och redundansfunktioner till att bygga din egen Azure-miljö. Metodtips hittar du i [Metodtips för Azure-tillgänglighet](/azure/architecture/checklist/resiliency-per-service).

