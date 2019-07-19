---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850332"
---
Att placera virtuella datorer i en enda region minskar det fysiska avståndet mellan instanserna. Att placera dem i en enda tillgänglighets zon kommer också att ta dem fysiskt närmare varandra. I takt med att Azure-utrymmet växer kan en enda tillgänglighets zon sträcka sig över flera fysiska data Center, vilket kan leda till en nätverks fördröjning som påverkar ditt program. 

För att få virtuella datorer så nära som möjligt, vilket ger lägsta möjliga fördröjning, bör du distribuera dem i en närhets placerings grupp.

En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Placerings grupper för närhet är användbara för arbets belastningar där låg latens är ett krav.


- Låg latens mellan fristående virtuella datorer.
- Låg latens mellan virtuella datorer i en enda tillgänglighets uppsättning eller en skalnings uppsättning för virtuella datorer. 
- Låg latens mellan fristående virtuella datorer, virtuella datorer i flera tillgänglighets uppsättningar eller flera skalnings uppsättningar. Du kan ha flera beräknings resurser i en enda placerings grupp för att sammanföra ett program med flera nivåer. 
- Låg latens mellan flera program nivåer med olika maskin varu typer. Du kan till exempel köra Server delen med M-serien i en tillgänglighets uppsättning och klient delen på en D-seriens instans i en skalnings uppsättning i en enda närhets placerings grupp.

> [!IMPORTANT]
> Närhets placerings grupper finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Placerings grupper för närhet är inte tillgängliga i dessa regioner under för hands versionen: **Östra Japan**, östra **Australien** och **Indien, centrala**.


## <a name="best-practices"></a>Bästa praxis 
- Använd närhets placerings grupper tillsammans med accelererat nätverk för lägsta latens. Mer information finns i [skapa en virtuell Linux-dator med accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [skapa en virtuell Windows-dator med accelererat nätverk](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Distribuera alla VM-storlekar i en enda Resource Manager-mall. För att undvika landning på maskin vara som inte stöder alla VM-SKU: er och storlekar som du behöver, måste du ta med alla program nivåer i en enda mall så att de kan distribueras på samma gång.
- Om du kör skript för distributionen med PowerShell, CLI eller SDK kan du få ett allokeringsfel `OverconstrainedAllocationRequest`. I det här fallet bör du stoppa/frigöra alla befintliga virtuella datorer och ändra ordningen i distributions skriptet så att den börjar med VM-SKU: n/storlekarna som misslyckades. 
- När du återanvänder en befintlig placerings grupp som de virtuella datorerna togs bort från, väntar du tills borttagningen har slutförts innan du lägger till virtuella datorer i den.
- Om svars tiden är din första prioritet ska du placera virtuella datorer i en närhets placerings grupp och hela lösningen i en tillgänglighets zon. Men om återhämtning är din högsta prioritet sprider du dina instanser över flera tillgänglighets zoner (en enda närhets placerings grupp kan inte omfatta zoner).