---
title: ta med fil
description: ta med fil
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 7/26/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: fc98206b5940ce75ddf26cfd905b1e0a89e2d85c
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326252"
---
| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| Backend-pool-storlek | Har stöd för upp till 1000 instanser | Har stöd för upp till 100 instanser |
| Slutpunkter för backend-pool | alla virtuella datorer i ett enda virtuellt nätverk, inklusive blandning av virtuella datorer, tillgänglighetsuppsättningar, VM-skalningsuppsättningar. | Virtuella datorer i en enda tillgänglighet eller en virtuell dator skala in. |
| Tillgänglighetszoner | I grundläggande SKU zonredundant och zonindelade klienter för inkommande och utgående, utgående flöden mappningar överleva zon fel, belastningsutjämning mellan zoner. | Saknas|
| Diagnostik | Azure Monitor flerdimensionella mått, till exempel byte och paket räknare, hälsa avsökning status, anslutningsförsök (TCP SYN), utgående anslutning health (SNAT lyckade och misslyckade flöden), aktiva data plan mätning av faktisk användning | Azure Log Analytics för offentlig Load Balancer, SNAT överbelastning avisering backend-pool hälsotillstånd antal. |
| Hög tillgänglighet portar | Intern lastbalanserare | Saknas |
| Säker som standard | Standard stängd för offentliga IP-adress och belastningsutjämnare slutpunkter och en nätverkssäkerhetsgrupp måste användas för att uttryckligen lista över tillåtna för trafik kan flöda. | Standard öppen, nätverkssäkerhetsgruppen som är valfritt. |
| [Utgående anslutningar](../articles/load-balancer/load-balancer-outbound-connections.md) | Flera klienter med per avanmälan av regeln för belastningsutjämning. Ett scenario för utgående _måste_ uttryckligen skapas för den virtuella datorn för att kunna använda utgående anslutning.  Tjänstslutpunkter i virtuella nätverk kan nås utan utgående anslutning och räknas inte mot data som bearbetas.  Alla offentliga IP-adresser, inklusive Azure PaaS-tjänster som är inte tillgängligt som VNet-tjänstslutpunkter, måste uppnås via utgående anslutning och antal för data som bearbetas. Utgående anslutningar via standard SNAT är inte tillgängliga när bara en intern belastningsutjämnare fungerar som värd för en virtuell dator. Utgående SNAT-programmering är transportprotokollet specifika baserat på protokollet av inkommande regel för belastningsutjämning. | Enkel klientdel, slumpmässigt valda när det finns flera klienter.  När endast den interna belastningsutjämnaren fungerar som värd för en virtuell dator, används standardvärdet SNAT. |
| [Flera klienter](../articles/load-balancer/load-balancer-multivip-overview.md) | Inkommande och [utgående](../articles/load-balancer/load-balancer-outbound-connections.md) | Endast inkommande |
| [Hälsoavsökning ned beteende](../articles/load-balancer/load-balancer-custom-probe-overview.md) | TCP-anslutningarna förblir aktiva på instansen avsökning av __och__ på alla avsökningar ned. | TCP-anslutningarna förblir aktiva på instansen avsökningen ned. Att avsluta alla TCP-anslutningar på alla avsökningar ned. |
| Hanteringsåtgärder | De flesta åtgärder < 30 sekunder | 60 – 90 sekunder vanliga. |
| SLA | 99,99% för sökvägen med två felfria virtuella datorer. | Implicit i VM-serviceavtal. | 
| Prissättning | Debiteras baserat på antalet regler kan data som bearbetas för inkommande eller utgående som är associerad med resurs.  | utan kostnad |
|  |  |  |