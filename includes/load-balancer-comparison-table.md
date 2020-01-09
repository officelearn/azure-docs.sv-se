---
title: ta med fil
description: ta med fil
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: d694a7030f96ef8e652d76cfb6036674aaa71249
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660229"
---
| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| Backend-Poolens storlek | Stöder upp till 1000 instanser. | Stöder upp till 100 instanser. |
| Backend-slutpunkter för poolen | Alla virtuella datorer i ett enda virtuellt nätverk, inklusive blandningar av virtuella datorer, tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer. | Virtuella datorer i en enskild tillgänglighetsuppsättning eller en VM-skalningsuppsättning. |
| [Hälsoavsökningar](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Beteende hos hälsoavsökning ur funktion](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-anslutningar är aktiva vid en instans avsökning __och__ i alla avsökningar. | TCP-anslutningar är vilande vid en instans avsökning. Alla TCP-anslutningar avslutas när alla avsökningar är nere. |
| Tillgänglighetszoner | Zone-redundant och zonindelade-frontend för inkommande och utgående trafik. Utgående flöden mappar överleva zon haveri. Belastnings utjämning mellan zoner. | Inte tillgänglig |
| Diagnostik | Azure Monitor. Flerdimensionella mått, inklusive byte och paket räknare. Status för hälso avsökning. Anslutnings försök (TCP-SYN). Utgående anslutnings hälsa (SNAT lyckade och misslyckade flöden). Mått för aktiva data planet. | Azure-Log Analytics endast för offentliga Load Balancer. Avisering om SNAT-överbelastning. Hälso antal för backend-pool. |
| HA-portar | Intern lastbalanserare | Inte tillgänglig |
| Säker som standard | Offentliga IP-adresser, offentliga Load Balancer slut punkter och interna Load Balancer slut punkter stängs till inkommande flöden om det inte tillåts av en nätverks säkerhets grupp. Observera att intern trafik från det virtuella nätverket till den interna belastningsutjämnaren fortfarande tillåts. | Öppnas som standard. Nätverks säkerhets gruppen är valfri. |
| [Utgående anslutningar](../articles/load-balancer/load-balancer-outbound-connections.md) | Du kan uttryckligen ange poolbaserad utgående NAT med [utgående regler](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Du kan välja att använda flera klient delar med belastnings Utjämnings regeln. Ett utgående scenario _måste_ skapas explicit för den virtuella datorn, tillgänglighets uppsättningen eller skalnings uppsättningen för den virtuella datorn så att den använder utgående anslutning. Tjänst slut punkter för virtuella nätverk kan nås utan att definiera utgående anslutning och räknas inte över till bearbetade data. Alla offentliga IP-adresser, inklusive Azure PaaS-tjänster som inte är tillgängliga som tjänst slut punkter för virtuella nätverk, måste nås med hjälp av utgående anslutning och räknas till bearbetade data. Om bara en intern Load Balancer hanterar en virtuell dator, tillgänglighets uppsättning eller skalnings uppsättning för virtuella datorer är utgående anslutningar över standard SNAT inte tillgängliga. Använd [utgående regler](../articles/load-balancer/load-balancer-outbound-rules-overview.md) i stället. Utgående SNAT-programmering beror på transport protokollet för inkommande belastnings Utjämnings regel. | Enkel klient del, väljs slumpmässigt när flera front-end-servrar finns. När endast interna Load Balancer hanterar en virtuell dator, tillgänglighets uppsättning eller skalnings uppsättning för virtuella datorer används standard SNAT. |
| [Regel för utgående trafik](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklarativ utgående NAT-konfiguration med hjälp av offentliga IP-adresser eller offentliga IP-prefix eller både och. Konfigurerbar timeout för utgående inaktivitet (4-120 minuter). Anpassad SNAT-port tilldelning | Inte tillgänglig |
| [TCP-återställning vid inaktivitet](../articles/load-balancer/load-balancer-tcp-reset.md) | Aktivera TCP-återställning (TCP RST) för timeout vid inaktivitet för alla regler | Inte tillgänglig |
| [Flera frontend-sidor](../articles/load-balancer/load-balancer-multivip-overview.md) | Inkommande och [utgående](../articles/load-balancer/load-balancer-outbound-connections.md) | Endast inkommande |
| Hanteringsåtgärder | De flesta åtgärder < 30 sekunder | 60-90 + sekunders standard |
| SLA | 99,99 % för datasökväg med två felfria virtuella datorer. | Inte aktuellt | 
| Prissättning | Debiteras baserat på antal regler och bearbetade inkommande och utgående data associerade med resursen. | Ingen avgift |
|  |  |  |
