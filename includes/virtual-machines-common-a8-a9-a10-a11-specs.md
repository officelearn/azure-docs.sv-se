---
title: ta med fil
description: ta med fil
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep;amverma
ms.custom: include file
ms.openlocfilehash: 5cbc19d5aade2bbcc8b8dca277352d1b17d1d35a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755174"
---
## <a name="deployment-considerations"></a>Distributionsöverväganden
* **Azure-prenumeration** – för att distribuera mer än ett par beräkningsintensiva instanser, Överväg att en användningsbaserad prenumeration eller andra alternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

* **Priser och tillgänglighet** -dessa VM-storlekar finns bara i Standard prisnivå. Kontrollera [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/) för tillgänglighet i Azure-regioner. 
* **Kärnkvot** – du kan behöva öka kärnkvoten i din Azure-prenumeration från standardvärdet. Din prenumeration kan också begränsa hur många kärnor du kan distribuera i vissa storleksfamiljer VM, inklusive H-serien. En kvot, [öppna en supportbegäran online-kund](../articles/azure-supportability/how-to-create-azure-support-request.md) utan kostnad. (Standardgränser kan variera beroende på din prenumerationskategori.)
  
  > [!NOTE]
  > Kontakta Azure-supporten om du har storskaliga kapacitetsbehoven. Azure-kvoter är kredit begränsar inte kapacitet garantier. Oavsett din kvot debiteras du bara för kärnor att du använder.
  > 
  > 
* **Virtuellt nätverk** – ett Azure [virtuellt nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) inte krävs för att använda beräkningsintensiva instanser. Men för många distributioner behöver du minst en molnbaserad Azure-nätverk eller en plats-till-plats-anslutning om du behöver komma åt lokala resurser. Skapa ett nytt virtuellt nätverk för att distribuera instanser när de behövs. Att lägga till beräkningsintensiva virtuella datorer i ett virtuellt nätverk i en tillhörighetsgrupp stöds inte.
* **Ändra storlek på** – på grund av deras specialiserad maskinvara kan du bara ändra storlek beräkningsintensiva instanser inom samma storleksfamilj (H-serien och beräkningsintensiva A-serien). Du kan till exempel bara ändra en H-serien virtuell dator från en H-serien storlek till en annan. Dessutom kan stöds ändra storlek på från en icke-beräkningsintensiva storlek till en storlek för beräkningsintensiva inte.  

## <a name="rdma-capable-instances"></a>RDMA-kompatibla instanser
En delmängd av beräkningsintensiva instanser (A8, A9, H16r, H16mr, HB och HC) har ett nätverksgränssnitt för direkt fjärråtkomst till minne minnesåtkomst (RDMA)-anslutning. Valda storlekar för N-serien som betecknas med ”r”, till exempel NC24rs-konfigurationer (NC24rs_v2 och NC24rs_v3) är också RDMA-kompatibla. Det här gränssnittet är ett tillägg till standard Azure nätverksgränssnittet tillgängliga för andra storlekar på Virtuella datorer. 
  
Det här gränssnittet kan RDMA-kompatibla förekomster att kommunicera över ett nätverk med InfiniBand (IB), fungerar EDR avgifterna för HB, HC, FDR för H16r, H16mr och RDMA-kompatibla N-serien virtuella datorer, och QDR för A8 och A9-datorer. Dessa funktioner för RDMA kan öka skalbarheten och prestandan i vissa program med Message Passing Interface (MPI). Mer information om hastighet, se information i tabeller på den här sidan.

> [!NOTE]
> I Azure, stöds bara IP över IB på SR-IOV-aktiverade virtuella datorer (för närvarande HB och HC). RDMA över IB stöds för alla RDMA-kompatibla förekomster.
>

