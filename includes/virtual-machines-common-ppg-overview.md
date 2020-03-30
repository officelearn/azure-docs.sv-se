---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73171090"
---
Om du placerar virtuella datorer i en enda region minskar det fysiska avståndet mellan instanserna. Placera dem inom en enda tillgänglighet zon kommer också att föra dem fysiskt närmare varandra. Men när Azure-fotavtrycket växer kan en enda tillgänglighetszon sträcka sig över flera fysiska datacenter, vilket kan resultera i en nätverksfördröjning som påverkar ditt program. 

Om du vill få virtuella datorer så nära som möjligt och uppnå lägsta möjliga svarstid bör du distribuera dem inom en närhetsplaceringsgrupp.

En närhetsplaceringsgrupp är en logisk gruppering som används för att se till att Azure-beräkningsresurser är fysiskt placerade nära varandra. Närhetsplaceringsgrupper är användbara för arbetsbelastningar där låg svarstid är ett krav.


- Låg latens mellan fristående virtuella datorer.
- Låg svarstid mellan virtuella datorer i en enda tillgänglighetsuppsättning eller en skaluppsättning för virtuella datorer. 
- Låg svarstid mellan fristående virtuella datorer, virtuella datorer i flera tillgänglighetsuppsättningar eller flera skalningsuppsättningar. Du kan ha flera beräkningsresurser i en enda placeringsgrupp för att samla ett program med flera nivåer. 
- Låg latens mellan flera programnivåer med olika maskinvarutyper. Till exempel kör serverdelen med M-serien i en tillgänglighetsuppsättning och klientdelen på en D-serieinstans, i en skalningsuppsättning, i en enda närhetsplaceringsgrupp.


![Grafik för närhetsplaceringsgrupper](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Använda grupper för närhetsplacering 

En närhetsplaceringsgrupp är en ny resurstyp i Azure. Du måste skapa en innan du använder den med andra resurser. När den har skapats kan den användas med virtuella datorer, tillgänglighetsuppsättningar eller skaluppsättningar för virtuella datorer. Du anger en närhetsplaceringsgrupp när du skapar beräkningsresurser som tillhandahåller närhetsplaceringsgrupp-ID. 

Du kan också flytta en befintlig resurs till en närhetsplaceringsgrupp. När du flyttar en resurs till en närhetsplaceringsgrupp bör du stoppa (deallocate) tillgången först eftersom den kommer att distribueras om eventuellt till ett annat datacenter i regionen så uppfyller samlokaliseringsbegränsningen. 

När det gäller tillgänglighetsuppsättningar och skaluppsättningar för virtuella datorer bör du ange närhetsplaceringsgruppen på resursnivå i stället för de enskilda virtuella datorerna. 

En närhetsplaceringsgrupp är ett samlokaliseringsvillkor snarare än en fästmekanism. Den fästs i ett visst datacenter med distributionen av den första resursen som ska användas. När alla resurser som använder proximity placement-gruppen har stoppats (deallocated) eller tagits bort, är det inte längre fäst. När du använder en närhetsplaceringsgrupp med flera VM-serier är det därför viktigt att ange alla nödvändiga typer i förväg i en mall när det är möjligt eller följa en distributionssekvens som förbättrar dina chanser för en lyckad distribution. Om distributionen misslyckas startar du om distributionen med den virtuella datorns storlek som har misslyckats som den första storleken som ska distribueras.


## <a name="best-practices"></a>Bästa praxis 
- Använd närhetsplaceringsgrupper tillsammans med accelererade nätverk för den lägsta svarstiden. Mer information finns i [Skapa en virtuell Linux-dator med Accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller Skapa en virtuell [Windows-dator med accelererat nätverk](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Distribuera alla VM-storlekar i en enda mall. För att undvika att landa på maskinvara som inte stöder alla vm-SKU:er och storlekar som du behöver, inkludera alla programnivåer i en enda mall så att de alla distribueras samtidigt.
- Om du skriptar distributionen med PowerShell, CLI eller SDK `OverconstrainedAllocationRequest`kan du få ett allokeringsfel . I det här fallet bör du stoppa/frigöra alla befintliga virtuella datorer och ändra sekvensen i distributionsskriptet så att den börjar med vm SKU/-storlekarna som misslyckades. 
- När du återanvänder en befintlig placeringsgrupp som virtuella datorer togs bort från väntar du på att borttagningen ska slutföras helt innan du lägger till virtuella datorer i den.
- Om svarstid är din första prioritet placerar du virtuella datorer i en närhetsplaceringsgrupp och hela lösningen i en tillgänglighetszon. Men om återhämtning är din högsta prioritet, sprida dina instanser över flera tillgänglighetszoner (en enda närhetsplaceringsgrupp kan inte spänna över zoner).