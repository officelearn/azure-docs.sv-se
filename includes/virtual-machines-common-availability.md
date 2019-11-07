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
ms.openlocfilehash: ffe0d3a85006bbaad53e471a10b10fb1bbea89b1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664441"
---
Den här artikeln ger en översikt över tillgänglighets funktionerna i Azure Virtual Machines (VM).

## <a name="high-availability"></a>Hög tillgänglighet

Arbets belastningar är vanligt vis spridda över olika virtuella datorer för att få hög genom strömning, prestanda och för att skapa redundans om en virtuell dator påverkas på grund av en uppdatering eller en händelse. 

Det finns några alternativ som Azure erbjuder för att uppnå hög tillgänglighet. Först ska vi prata om grundläggande konstruktioner. 

### <a name="availability-zones"></a>Tillgänglighetszoner

[Tillgänglighets zoner](../articles/availability-zones/az-overview.md) utökar kontroll nivån som du måste ha för att upprätthålla tillgängligheten för program och data på dina virtuella datorer. En tillgänglighets zon är en fysiskt separat zon i en Azure-region. Det finns tre Tillgänglighetszoner per Azure-region som stöds. 

Varje tillgänglighetszon har en distinkt strömkälla, nätverk och kylning. Genom att skapa lösningar för att använda replikerade virtuella datorer i zoner kan du skydda dina appar och data från förlust av ett Data Center. Om en zon komprometteras, är replikerade appar och data omedelbart tillgängliga i en annan zon. 

![Tillgänglighetszoner](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Lär dig mer om att distribuera en virtuell [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) -eller [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) -dator i en tillgänglighets zon.


### <a name="fault-domains"></a>Feldomäner

En feldomän är en logisk grupp av underliggande maskinvara som delar en gemensam strömkälla och nätverksswitch, ungefär som ett rack i ett lokalt datacenter. 

### <a name="update-domains"></a>Uppdateringsdomäner

En uppdateringsdomän är en logisk grupp av underliggande maskinvara som kan underhållas eller startas om samtidigt. 

På så sätt säkerställs att minst en instans av ditt program alltid körs vid ett periodiskt underhåll av Azure-plattformen. Ordningen för uppdaterings domäner som startas om kanske inte fortsätter i följd under underhåll, men endast en uppdaterings domän startas om i taget.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines skalnings uppsättningar 

Med skalnings uppsättningar för virtuella Azure-datorer kan du skapa och hantera en grupp med belastningsutjämnade virtuella datorer. Antal VM-instanser kan automatiskt öka eller minska som svar på efterfrågan eller ett definierat schema. Skalnings uppsättningar ger hög tillgänglighet till dina program och gör att du kan hantera, konfigurera och uppdatera många virtuella datorer centralt. Vi rekommenderar att två eller flera virtuella datorer skapas i en skalnings uppsättning för att tillhandahålla ett program med hög tillgänglighet och som uppfyller [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Du betalar ingen kostnad för själva skalnings uppsättningen. du betalar bara för varje VM-instans som du skapar. När en enskild virtuell dator använder [Azure Premium-SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)gäller Azure SLA för oplanerade underhålls händelser. Virtuella datorer i en skalnings uppsättning kan distribueras över flera regioner och fel domäner för att maximera tillgängligheten och återhämtning till drift störningar på grund av avbrott i data centret och planerade eller oplanerade underhålls händelser. Virtuella datorer i en skalnings uppsättning kan också distribueras till en enda tillgänglighets zon eller regionalt. Distributions alternativen för tillgänglighets zon kan variera beroende på dirigerings läge.

### <a name="preview-orchestration-mode-preview"></a>Förhands granskning: för hands version av Orchestration-läge
Med skalnings uppsättningar för virtuella datorer kan du ange Orchestration-läge.  Med den virtuella datorns läge för skalnings uppsättning (förhands granskning) kan du nu välja om skalnings uppsättningen ska dirigera virtuella datorer som uttryckligen skapas utanför en konfigurations modell för skalnings uppsättningar eller om virtuella dator instanser har skapats implicit baserat på konfigurations modellen. Välj det Orchestration-läge som den virtuella datorns Orchestration-modell tillåter att du grupperar explicit definierade Virtual Machines tillsammans i en region eller i en tillgänglighets zon. Virtuella datorer som distribueras i en tillgänglighets zon ger zonindelade isolering till virtuella datorer är de kopplade till tillgänglighets zonens gränser och omfattas inte av fel som kan uppstå i andra tillgänglighets zoner i regionen. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Konfigurations modell för virtuell dator| Ingen. VirtualMachineProfile är odefinierat i skalnings uppsättnings modellen. | Krävs. VirtualMachineProfile är ifyllt i skalnings uppsättnings modellen. |
| Lägger till ny virtuell dator i skalnings uppsättningen| Virtuella datorer läggs explicit till i skalnings uppsättningen när den virtuella datorn skapas. | Virtuella datorer skapas implicit och läggs till i skalnings uppsättningen baserat på den virtuella datorns konfigurations modell, instans antal och regler för automatisk skalning. |
| Tillgänglighetszoner| Stöder regional distribution eller virtuella datorer i en tillgänglighets zon| Stöder regional distribution eller flera Tillgänglighetszoner; Kan definiera strategi för zon utjämning |
| Feldomäner| Kan definiera antalet fel domäner. 2 eller 3 baserat på regional support och 5 för tillgänglighets zon. Den tilldelade VM-feldomänen behålls med VM-livscykeln, inklusive frigör och starta om. | Kan definiera 1, 2 eller 3 fel domäner för icke-zonindelade distributioner och 5 för distributioner av tillgänglighets zoner. Den tilldelade virtuella dator fel domänen är inte beständig med VM-livscykeln, virtuella datorer tilldelas en feldomän vid tidpunkten för tilldelningen. |
| Uppdateringsdomäner| Ej tillämpligt. Uppdaterings domäner mappas automatiskt till fel domäner| Ej tillämpligt. Uppdaterings domäner mappas automatiskt till fel domäner |

**Fel domäner och uppdaterings domäner**

Skalnings uppsättningar för virtuella datorer fören klar designen för hög tillgänglighet genom att justera fel domäner och uppdatera domäner. Du behöver bara definiera antalet fel domäner för skalnings uppsättningen. Antalet fel domäner som är tillgängliga för skalnings uppsättningarna kan variera beroende på region. Se [antalet fel domäner per region](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region).


## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
En tillgänglighets uppsättning är en logisk gruppering av virtuella datorer i ett Data Center som gör det möjligt för Azure att förstå hur ditt program är utformat för att tillhandahålla redundans och tillgänglighet. Vi rekommenderar att två eller flera virtuella datorer skapas i en tillgänglighets uppsättning för att tillhandahålla ett program med hög tillgänglighet och uppfylla [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För själva tillgänglighets uppsättningen kostar du bara att betala för varje VM-instans som du skapar. När en enskild virtuell dator använder [Azure Premium-SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)gäller Azure SLA för oplanerade underhålls händelser.

I en tillgänglighets uppsättning distribueras virtuella datorer automatiskt mellan dessa fel domäner. På så sätt begränsas påverkan av potentiella fel på fysisk maskinvara, nätverksavbrott och strömavbrott.

För virtuella datorer som använder [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) justeras de virtuella datorerna efter feldomänerna för hanterade diskar när en hanterad tillgänglighetsuppsättning används. På så sätt säkerställs att alla hanterade diskar som är kopplade till en virtuell dator finns i samma feldomän. 

Endast virtuella datorer med hanterade diskar kan skapas i en hanterad tillgänglighetsuppsättning. Antalet feldomäner kan vara två eller tre, beroende på region. Du kan läsa mer om dessa hanterade disk fel domäner för virtuella [Linux-datorer](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) eller [virtuella Windows-datorer](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Hanterad tillgänglighets uppsättning](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Virtuella datorer i en tillgänglighets uppsättning distribueras också automatiskt mellan uppdaterings domäner. 

![Tillgänglighetsuppsättningar](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Nästa steg
Nu kan du börja använda dessa tillgänglighets- och redundansfunktioner till att bygga din egen Azure-miljö. Metodtips hittar du i [Metodtips för Azure-tillgänglighet](../articles/best-practices-availability-checklist.md).

