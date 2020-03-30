---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187296"
---
Det är viktigt att inse att det finns två sätt att konfigurera en tillgänglighetsgruppavlyssnare i Azure. Hur skiljer sig i vilken typ av Azure belastningsutjämnare du använder när du skapar lyssnaren. I följande tabell beskrivs skillnaderna:

| Typ av belastningsutjämning | Implementering | Använd när: |
| --- | --- | --- |
| **Externa** |Använder den *offentliga virtuella IP-adressen* för molntjänsten som är värd för virtuella datorer.Uses the public virtual IP address of the cloud service that hosts the virtual machines (VMs). |Du måste komma åt lyssnaren utanför det virtuella nätverket, inklusive från Internet. |
| **Intern** |Använder en *intern belastningsutjämnare* med en privat adress för lyssnaren. |Du kan bara komma åt lyssnaren från samma virtuella nätverk. Den här åtkomsten inkluderar VPN från plats till plats i hybridscenarier. |

> [!IMPORTANT]
> För en lyssnare som använder molntjänstens offentliga VIP (extern belastningsutjämnare), så länge klienten, lyssnaren och databaserna finns i samma Azure-region, ådrar du dig inte utgående avgifter. Annars betraktas alla data som returneras via lyssnaren som utgående och debiteras vid normala dataöverföringshastigheter. 
> 
> 

En ILB kan bara konfigureras i virtuella nätverk med ett regionalt scope. Befintliga virtuella nätverk som har konfigurerats för en tillhörighetsgrupp kan inte använda en ILB. Mer information finns i [Översikt över intern belastningsutjämnare](../articles/load-balancer/load-balancer-internal-overview.md).

