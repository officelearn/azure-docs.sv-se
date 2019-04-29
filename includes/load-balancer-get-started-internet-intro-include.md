---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517009"
---
En Azure Load Balancer är en Layer 4-lastbalanserare (TCP, UDP). Lastbalanseraren ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfria tjänstinstanser i molntjänster eller virtuella datorer i en lastbalanseringsuppsättning. Azure Load Balancer kan även presentera dessa tjänster på flera portar, flera IP-adresser eller både och.

Du kan konfigurera en lastbalanserare för att:

* Belastningsutjämna inkommande Internettrafik till virtuella datorer (VM). Vi refererar till en lastbalanserare i det här scenariot som en [Internetuppkopplad lastbalanserare](../articles/load-balancer/load-balancer-internet-overview.md).
* Belastningsutjämna trafik mellan virtuella datorer i ett virtuellt nätverk (VNet), mellan virtuella datorer i molntjänster eller mellan lokala datorer och virtuella datorer i ett virtuellt nätverk mellan olika platser. Vi refererar till en lastbalanserare i det här scenariot som en [Intern lastbalanserare (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
* Vidarebefordra extern trafik till en viss VM-instans.