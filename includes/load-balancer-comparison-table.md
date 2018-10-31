---
title: ta med fil
description: ta med fil
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 10/29/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 5517cd236c503edce88ab45edbeff5604ef9322b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50254575"
---
| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| Backend-pool-storlek | Har stöd för upp till 1000 instanser | Har stöd för upp till 100 instanser |
| Slutpunkter för backend-pool | alla virtuella datorer i ett enda virtuellt nätverk, inklusive blandning av virtuella datorer, tillgänglighetsuppsättningar, VM-skalningsuppsättningar. | Virtuella datorer i en enda tillgänglighet eller en virtuell dator skala in. |
| [Hälsoavsökningar](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Hälsoavsökning ned beteende](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-anslutningarna förblir aktiva på instansen avsökning av __och__ på alla avsökningar ned. | TCP-anslutningarna förblir aktiva på instansen avsökningen ned. TCP-anslutningar avslutas alla på alla avsökningar är nere. |
| Tillgänglighetszoner | I Standard-SKU, zonredundant och zonindelade klienter för inkommande och utgående, överlever utgående flöden mappningar zon fel, belastningsutjämning mellan zoner. | Inte tillgänglig |
| Diagnostik | Azure Monitor flerdimensionella mått, till exempel byte och paket räknare, hälsa avsökning status, anslutningsförsök (TCP SYN), utgående anslutning health (SNAT lyckade och misslyckade flöden), aktiva data plan mätning av faktisk användning | Azure Log Analytics för offentlig Load Balancer, SNAT överbelastning avisering backend-pool hälsotillstånd antal. |
| Hög tillgänglighet portar | Intern lastbalanserare | Inte tillgängligt |
| Säker som standard | Använd standard inkommande stängd för offentliga IP-adress och belastningsutjämnare slutpunkter och en nätverkssäkerhetsgrupp uttryckligen godkänna för trafik. | Standard öppen, nätverkssäkerhetsgruppen som är valfritt. |
| [Utgående anslutningar](../articles/load-balancer/load-balancer-outbound-connections.md) | Du kan uttryckligen ange pool-baserade utgående NAT med [utgående regler](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Du kan använda flera klienter med per Läs in belastningsutjämning regeln avstår. Ett scenario för utgående _måste_ uttryckligen skapas för den virtuella datorn att använda utgående anslutning.  Tjänstslutpunkter i virtuella nätverk kan nås utan utgående anslutning och räknas inte mot data som bearbetas.  Alla offentliga IP-adresser, inklusive Azure PaaS-tjänster som är inte tillgängligt som VNet-tjänstslutpunkter, måste uppnås via utgående anslutning och antal för data som bearbetas. När bara en intern belastningsutjämnare fungerar som värd för en virtuell dator, utgående anslutningar via standard SNAT inte användas. Använd [utgående regler](../articles/load-balancer/load-balancer-outbound-rules-overview.md) i stället. Utgående SNAT-programmering är transportprotokollet specifika baserat på protokollet av inkommande regel för belastningsutjämning. | Enkel klientdel, slumpmässigt valda när det finns flera klienter.  När endast den interna belastningsutjämnaren fungerar som värd för en virtuell dator, används standardvärdet SNAT. |
| [Utgående regler](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklarativ utgående NAT-konfigurationen, inklusive vilka offentlig IP-adress eller offentlig IP-adressprefix, konfigurerbara utgående timeout för inaktivitet, anpassade SNAT-porttilldelning | Inte tillgängligt |
|  [TCP-återställning på inaktiv](../articles/load-balancer/load-balancer-tcp-reset.md) | Aktivera TCP nollställs Timeout för inaktivitet (TCP RSTA) på alla regler | Inte tillgängligt |
| [Flera klienter](../articles/load-balancer/load-balancer-multivip-overview.md) | Inkommande och [utgående](../articles/load-balancer/load-balancer-outbound-connections.md) | Endast inkommande |
| Hanteringsåtgärder | De flesta åtgärder < 30 sekunder | 60 – 90 sekunder vanliga. |
| SLA | 99,99% för sökvägen med två felfria virtuella datorer. | [Implicit i VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). | 
| Prissättning | Debiteras baserat på antalet regler kan data som bearbetas för inkommande eller utgående som är associerad med resurs.  | utan kostnad |
|  |  |  |
