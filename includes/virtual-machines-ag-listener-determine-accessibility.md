---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097720"
---
Det är viktigt att det finns två sätt att konfigurera en tillgänglighetsgruppslyssnare i Azure. Hur skiljer sig åt i typ av Azure belastningsutjämnare som du använder när du skapar lyssnaren. I följande tabell beskrivs skillnaderna:

| Typ av belastningsutjämnare | Implementering | Använd när: |
| --- | --- | --- |
| **Externt** |Använder den *offentliga virtuella IP-adressen* av Molntjänsten som är värd för virtuella datorer (VM). |Du behöver åtkomst till lyssnaren från utanför det virtuella nätverket, inklusive från Internet. |
| **Intern** |Använder en *intern belastningsutjämnare* med en privat adress för lyssnaren. |Du kan öppna lyssnaren endast från inom samma virtuella nätverk. Den här åtkomsten innehåller plats-till-plats-VPN i hybridsituationer. |

> [!IMPORTANT]
> För en lyssnare som använder du cloud Services offentlig VIP (extern belastningsutjämnare), så länge klienten, lyssnaren och databaser finns i samma Azure-region, tillkommer kostnader för utgående trafik. I annat fall alla data som returneras via lyssnaren anses utgående trafik och den debiteras enligt normal dataöverföring under. 
> 
> 

En ILB kan endast konfigureras på virtuella nätverk med regional omfattning. Befintliga virtuella nätverk som har konfigurerats för en tillhörighetsgrupp kan inte använda en ILB. Mer information finns i [översikt över intern belastningsutjämnare](../articles/load-balancer/load-balancer-internal-overview.md).

<!-- Update_Description: update meta properties -->