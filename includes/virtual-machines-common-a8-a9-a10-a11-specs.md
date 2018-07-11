---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep
ms.custom: include file
ms.openlocfilehash: 296e92d803bb69376f286aa60cfb4a955b08010f
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "34669362"
---
## <a name="deployment-considerations"></a>Distributionsöverväganden
* **Azure-prenumeration** – för att distribuera mer än ett par beräkningsintensiva instanser, Överväg att en användningsbaserad prenumeration eller andra alternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

* **Priser och tillgänglighet** -dessa VM-storlekar finns bara i Standard prisnivå. Kontrollera [produkttillgänglighet per region] (https://azure.microsoft.com/regions/services/) för tillgänglighet i Azure-regioner. 
* **Kärnkvot** – du kan behöva öka kärnkvoten i din Azure-prenumeration från standardvärdet. Din prenumeration kan också begränsa hur många kärnor du kan distribuera i vissa storleksfamiljer VM, inklusive H-serien. En kvot, [öppna en supportbegäran online-kund](../articles/azure-supportability/how-to-create-azure-support-request.md) utan kostnad. (Standardgränser kan variera beroende på din prenumerationskategori.)
  
  > [!NOTE]
  > Kontakta Azure-supporten om du har storskaliga kapacitetsbehoven. Azure-kvoter är kredit begränsar inte kapacitet garantier. Oavsett din kvot debiteras du bara för kärnor att du använder.
  > 
  > 
* **Virtuellt nätverk** – ett Azure [virtuellt nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) inte krävs för att använda beräkningsintensiva instanser. Men för många distributioner behöver du minst en molnbaserad Azure-nätverk eller en plats-till-plats-anslutning om du behöver komma åt lokala resurser. Skapa ett nytt virtuellt nätverk för att distribuera instanser när de behövs. Att lägga till beräkningsintensiva virtuella datorer i ett virtuellt nätverk i en tillhörighetsgrupp stöds inte.
* **Ändra storlek på** – på grund av deras specialiserad maskinvara kan du bara ändra storlek beräkningsintensiva instanser inom samma storleksfamilj (H-serien och beräkningsintensiva A-serien). Du kan till exempel bara ändra en H-serien virtuell dator från en H-serien storlek till en annan. Dessutom kan stöds ändra storlek på från en icke-beräkningsintensiva storlek till en storlek för beräkningsintensiva inte.  

## <a name="rdma-capable-instances"></a>RDMA-kompatibla förekomster
En delmängd av beräkningsintensiva instanser (H16r, H16mr, A8 och A9) har ett nätverksgränssnitt för direkt fjärråtkomst till minne minnesåtkomst (RDMA)-anslutning. (Storlekar för valda N-serien betecknas med ”r”, till exempel NC24r är också RDMA-kompatibla.) Det här gränssnittet är ett tillägg till standard Azure nätverksgränssnittet tillgängliga för andra storlekar på Virtuella datorer. 
  
Det här gränssnittet kan RDMA-kompatibla förekomster att kommunicera över ett nätverk med InfiniBand (IB), med priserna för FDR för H16r, H16mr och RDMA-kompatibla N-serien virtuella datorer, och QDR för A8 och A9-datorer. Dessa funktioner för RDMA kan öka skalbarheten och prestandan i vissa program med Message Passing Interface (MPI).

> [!NOTE]
> IP över IB stöds inte i Azure. Endast RDMA over IB stöds.
>

Distribuera RDMA-kompatibla HPC virtuella datorer i samma tillgänglighetsuppsättning eller VM-skalningsuppsättning (när du använder Azure Resource Manager-distributionsmodellen) eller samma molntjänst (när du använder den klassiska distributionsmodellen). Om du använder en VM-skalningsuppsättning kan du se till att du begränsa distributionen till en enda placeringsgrupp; till exempel i en Resource Manager-mall, ange den *singlePlacementGroup* egenskap *SANT*. Ytterligare krav för RDMA-kompatibla HPC virtuella datorer till Azure RDMA-nätverk följer.