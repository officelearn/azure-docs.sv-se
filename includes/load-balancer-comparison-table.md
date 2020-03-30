---
title: ta med fil
description: ta med fil
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78202472"
---
| | Standard belastningsutjämnare | Grundläggande belastningsutjämnare |
| --- | --- | --- |
| [Storlek på serverdelspool](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Stöder upp till 1000 instanser. | Stöder upp till 300 instanser. |
| Slutpunkter för serverdelspool | Alla virtuella datorer eller skala virtuella datorer i ett enda virtuellt nätverk. | Virtuella datorer i en enskild tillgänglighetsuppsättning eller en VM-skalningsuppsättning. |
| [Hälsotillståndsavsökningar](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Beteende hos hälsoavsökning ur funktion](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-anslutningar håller sig vid liv på en instansavsökning nedåt __och__ på alla sonder nedåt. | TCP-anslutningar håller sig vid liv på en instansavsökning nedåt. Alla TCP-anslutningar avslutas när alla avsökningar är nere. |
| Tillgänglighetszoner | Zonuppsagda och zonbaserade frontends för inkommande och utgående trafik. | Inte tillgängligt |
| Diagnostik | [Flerdimensionella mått i Azure Monitor](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Azure Monitor-loggar](../articles/load-balancer/load-balancer-monitor-log.md) |
| HA-portar | [Tillgänglig för intern belastningsutjämnare](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Inte tillgängligt |
| Säker som standard | Stängd för inkommande flöden om det inte tillåts av en nätverkssäkerhetsgrupp. Observera att intern trafik från det virtuella nätverket till den interna belastningsutjämnaren är tillåten. | Öppna som standard. Nätverkssäkerhetsgrupp är valfri. |
| Regler för utgående trafik | [Deklarativ utgående NAT-konfiguration](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Inte tillgängligt |
| TCP-återställning vid inaktivitet | [Tillgänglig på alla regler](../articles/load-balancer/load-balancer-tcp-reset.md) | Inte tillgängligt |
| [Flera främre ändar](../articles/load-balancer/load-balancer-multivip-overview.md) | Inkommande och [utgående](../articles/load-balancer/load-balancer-outbound-connections.md) | Endast inkommande |
| Hanteringsåtgärder | De flesta åtgärder < 30 sekunder | 60-90+ sekunder typiska |
| SLA | [99,99 %](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Inte tillgängligt | 
