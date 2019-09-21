---
title: ta med fil
description: ta med fil
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 0930fa371500125c15cd969b9e9f4b7a2853612e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174998"
---
## <a name="deployment-considerations"></a>Distributionsöverväganden
* **Azure-prenumeration** – om du vill distribuera fler än några få beräknings intensiva instanser bör du överväga att betala per användning-prenumeration eller andra köp alternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

* **Priser och tillgänglighet** – dessa VM-storlekar erbjuds endast på standard pris nivån. Kontrol lera [vilka produkter som är tillgängliga i region](https://azure.microsoft.com/global-infrastructure/services/) för tillgänglighet i Azure-regioner. 
* **Kärnor-kvot** – du kan behöva öka kvoten för kärnor i din Azure-prenumeration från standardvärdet. Din prenumeration kan också begränsa antalet kärnor som du kan distribuera i vissa storlekar på virtuella dator grupper, inklusive H-serien. Om du vill begära en kvot ökning [öppnar du en support förfrågan online](../articles/azure-supportability/how-to-create-azure-support-request.md) utan kostnad. (Standard gränserna kan variera beroende på din prenumerations kategori.)
  
  > [!NOTE]
  > Kontakta Azure-supporten om du har storskaliga kapacitets behov. Azure-kvoter är kredit gränser, inte kapacitets garantier. Oavsett din kvot debiteras du bara för kärnor som du använder.
  > 
  > 
* **Virtuellt nätverk** – ett virtuellt Azure- [nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) krävs inte för att använda beräknings intensiva instanser. För många distributioner behöver du dock minst ett molnbaserad virtuellt Azure-nätverk eller en plats-till-plats-anslutning om du behöver åtkomst till lokala resurser. När det behövs skapar du ett nytt virtuellt nätverk för att distribuera instanserna. Det går inte att lägga till beräknings intensiva virtuella datorer i ett virtuellt nätverk i en tillhörighets grupp.
* **Ändra storlek** – på grund av deras specialiserade maskin vara kan du bara ändra storlek på beräknings intensiva instanser inom samma storleks familj (H-serien eller beräknings intensiva a-serien). Du kan till exempel bara ändra storlek på en virtuell dator i H-serien från en storlek på H-serien till en annan. Det går inte heller att ändra storlek på en icke-Compute-intensiv storlek till en beräknings intensiv storlek.  

## <a name="rdma-capable-instances"></a>RDMA-kompatibla instanser
En del av beräknings intensiva instanserna (A8, A9, H16r, H16mr, HB och HC) är ett nätverks gränssnitt för anslutning till fjärrminne (RDMA). De valda N-serie storlekarna som anges med r, till exempel NC24rs-konfigurationer (NC24rs_v2 och NC24rs_v3) är också RDMA-kompatibla. Det här gränssnittet är förutom standard nätverks gränssnittet i Azure tillgängligt för andra VM-storlekar. 
  
Med det här gränssnittet kan de RDMA-kompatibla instanserna kommunicera över ett InfiniBand-nätverk, som arbetar med EDR-priser för HB, HC, FDR rates för H16r, H16mr och RDMA-kompatibla N-seriens virtuella datorer, samt QDR priser för A8 och A9 virtuella datorer. Dessa RDMA-funktioner kan öka skalbarheten och prestandan för vissa MPI-program (Message Passing Interface). Mer information om hastighet finns i informationen i tabellerna på den här sidan.

> [!NOTE]
> I Azure stöds endast IP över IB på de virtuella SR-IOV-datorerna (SR-IOV för InfiniBand, för närvarande HB och HC). RDMA över IB stöds för alla RDMA-kompatibla instanser.
>

