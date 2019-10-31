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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171090"
---
Att placera virtuella datorer i en enda region minskar det fysiska avståndet mellan instanserna. Att placera dem i en enda tillgänglighets zon kommer också att ta dem fysiskt närmare varandra. I takt med att Azure-utrymmet växer kan en enda tillgänglighets zon sträcka sig över flera fysiska data Center, vilket kan leda till en nätverks fördröjning som påverkar ditt program. 

För att få virtuella datorer så nära som möjligt, vilket ger lägsta möjliga fördröjning, bör du distribuera dem i en närhets placerings grupp.

En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Placerings grupper för närhet är användbara för arbets belastningar där låg latens är ett krav.


- Låg latens mellan fristående virtuella datorer.
- Låg latens mellan virtuella datorer i en enda tillgänglighets uppsättning eller en skalnings uppsättning för virtuella datorer. 
- Låg latens mellan fristående virtuella datorer, virtuella datorer i flera tillgänglighets uppsättningar eller flera skalnings uppsättningar. Du kan ha flera beräknings resurser i en enda placerings grupp för att sammanföra ett program med flera nivåer. 
- Låg latens mellan flera program nivåer med olika maskin varu typer. Du kan till exempel köra Server delen med M-serien i en tillgänglighets uppsättning och klient delen på en D-seriens instans i en skalnings uppsättning i en enda närhets placerings grupp.


![Bild för närhets placerings grupper](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Använda närhets placerings grupper 

En närhets placerings grupp är en ny resurs typ i Azure. Du måste skapa ett innan du använder det med andra resurser. När den har skapats kan den användas med virtuella datorer, tillgänglighets uppsättningar eller skalnings uppsättningar för virtuella datorer. Du anger en närhets placerings grupp när du skapar beräknings resurser som ger närhets grupp-ID: t. 

Du kan också flytta en befintlig resurs till en närhets placerings grupp. När du flyttar en resurs till en närhets placerings grupp bör du stoppa (frigöra) till gången först eftersom den kommer att omdistribueras till ett annat data Center i regionen så att den uppfyller samplacerings begränsningen. 

Om det gäller tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer bör du ange placerings gruppen närhet på resurs nivå i stället för de enskilda virtuella datorerna. 

En närhets placerings grupp är en samplacerings begränsning snarare än en fäst funktion. Den fästs i ett Data Center med distributionen av den första resursen för att använda den. När alla resurser som använder närhets placerings gruppen har stoppats (frigjorts) eller tagits bort, är de inte längre fästa. När du använder en närhets placerings grupp med flera VM-serier är det därför viktigt att du anger alla nödvändiga typer som är aktiva i en mall när det är möjligt eller följer en distributions ordning som förbättrar risken för en lyckad distribution. Om distributionen Miss lyckas startar du om distributionen med den VM-storlek som har misslyckats som den första storleken som ska distribueras.


## <a name="best-practices"></a>Bästa metoder 
- Använd närhets placerings grupper tillsammans med accelererat nätverk för lägsta latens. Mer information finns i [skapa en virtuell Linux-dator med accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [skapa en virtuell Windows-dator med accelererat nätverk](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Distribuera alla VM-storlekar i en enda mall. För att undvika landning på maskin vara som inte stöder alla VM-SKU: er och storlekar som du behöver, måste du ta med alla program nivåer i en enda mall så att de kan distribueras på samma gång.
- Om du kör skript för distributionen med PowerShell, CLI eller SDK kan du få ett allokeringsfel `OverconstrainedAllocationRequest`. I det här fallet bör du stoppa/frigöra alla befintliga virtuella datorer och ändra ordningen i distributions skriptet så att den börjar med VM-SKU: n/storlekarna som misslyckades. 
- När du återanvänder en befintlig placerings grupp som de virtuella datorerna togs bort från, väntar du tills borttagningen har slutförts innan du lägger till virtuella datorer i den.
- Om svars tiden är din första prioritet ska du placera virtuella datorer i en närhets placerings grupp och hela lösningen i en tillgänglighets zon. Men om återhämtning är din högsta prioritet sprider du dina instanser över flera tillgänglighets zoner (en enda närhets placerings grupp kan inte omfatta zoner).