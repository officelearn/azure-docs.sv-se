---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ee32886ddb74bdbbe0f240310629c8ef26230a68
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
## <a name="deployment-considerations"></a>Distributionsöverväganden
* **Azure-prenumeration** – för att distribuera flera instanser av beräkningsintensiva bör du överväga att en prenumeration med användningsbaserad betalning eller andra köpalternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

* **Priser och tillgänglighet** -storlekar för dessa Virtuella erbjuds endast i vanlig prisnivån. Kontrollera [produkter som är tillgängliga efter region] (https://azure.microsoft.com/regions/services/) för tillgänglighet i Azure-regioner. 
* **Kärnor kvoten** – du kan behöva öka kvoten kärnor i din Azure-prenumeration från standardvärdet. Din prenumeration kan också begränsa antal kärnor som du kan distribuera i vissa VM storlek familjer, inklusive H-serien. Att begära en ökad kvot [öppna en supportbegäran online customer](../articles/azure-supportability/how-to-create-azure-support-request.md) utan kostnad. (Standardgränser kan variera beroende på din prenumerationskategori.)
  
  > [!NOTE]
  > Kontakta Azure-supporten om du har stora kapacitetsbehov. Azure-kvoter är kredit begränsar inte kapacitet garantier. Oavsett din kvot endast debiteras du för kärnor att du använder.
  > 
  > 
* **Virtuellt nätverk** – en Azure [virtuellt nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) inte krävs för att använda beräkningsintensiva instanser. Däremot för många distributioner behöver du minst en molnbaserad Azure virtuellt nätverk eller en plats-till-plats-anslutning om du behöver få åtkomst till lokala resurser. Skapa ett nytt virtuellt nätverk för att distribuera instanser vid behov. Lägga till beräkningsintensiva virtuella datorer till ett virtuellt nätverk i en tillhörighetsgrupp stöds inte.
* **Ändra storlek på** – på grund av deras speciell maskinvara, du kan bara ändra storlek beräkningsintensiva instanser inom samma familj (H-serien eller beräkningsintensiva A-serien) i storlek. Du kan till exempel bara ändra en H-serien virtuell dator från en H-storlek till en annan. Dessutom stöds storleksändring från en icke-beräkningsintensiva storlek till en beräkningsintensiva storlek inte.  

## <a name="rdma-capable-instances"></a>RDMA-kompatibla instanser
En delmängd av beräkningsintensiva instanser (H16r, H16mr, A8 och A9) har ett nätverksgränssnitt för remote direct memory access (RDMA)-anslutning. (Valda N-serien storlekar betecknats med ”r”, till exempel NC24r är också RDMA-kompatibla.) Det här gränssnittet är utöver standard Azure nätverksgränssnittet tillgänglig för andra storlekar på VM. 
  
Du kan använda RDMA-kompatibla instanserna kommunicera InfiniBand (IB) nätverk, arbetar med FDR priser för H16r, H16mr och RDMA-kompatibla N-serien virtuella datorer och QDR priser för A8 och A9 virtuella datorer i gränssnittet. Dessa funktioner för RDMA kan öka den skalbarhet och prestanda för vissa program för Message Passing Interface (MPI).

> [!NOTE]
> IP över IB stöds inte i Azure. Endast RDMA över IB stöds.
>

Distribuera RDMA-kompatibla HPC virtuella datorer i samma tillgänglighetsuppsättning eller skaluppsättningen för virtuell dator (när du använder Azure Resource Manager-distributionsmodellen) eller samma tjänst i molnet (när du använder den klassiska distributionsmodellen). Ytterligare krav för RDMA-kompatibla HPC virtuella datorer får åtkomst till Azure RDMA-nätverket följer.