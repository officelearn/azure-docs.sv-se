---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "67187296"
---
Det är viktigt att du inser att det finns två sätt att konfigurera en tillgänglighets grupps lyssnare i Azure. Olika sätt skiljer sig åt i vilken typ av Azure-belastningsutjämnare du använder när du skapar lyssnaren. I följande tabell beskrivs skillnaderna:

| Belastnings Utjämnings typ | Implementering | Använd när: |
| --- | --- | --- |
| **5,25** |Använder den *offentliga virtuella IP-adressen* för den moln tjänst som är värd för de virtuella datorerna (VM). |Du måste komma åt lyssnaren utanför det virtuella nätverket, inklusive från Internet. |
| **Intern** |Använder en *intern belastningsutjämnare* med en privat adress för lyssnaren. |Du kan bara komma åt lyssnaren i samma virtuella nätverk. Den här åtkomsten inkluderar plats-till-plats-VPN i hybrid scenarier. |

> [!IMPORTANT]
> För en lyssnare som använder moln tjänstens offentliga VIP (extern belastningsutjämnare), så länge klienten, lyssnaren och databaserna finns i samma Azure-region debiteras du inte utgående kostnader. Annars betraktas alla data som returneras via lyssnaren ut och de debiteras enligt normala priser för data överföring. 
> 
> 

En ILB kan bara konfigureras på virtuella nätverk med ett regionalt omfång. Befintliga virtuella nätverk som har kon figurer ATS för en tillhörighets grupp kan inte använda en ILB. Mer information finns i [Översikt över intern belastnings utjämning](../articles/load-balancer/load-balancer-internal-overview.md).

