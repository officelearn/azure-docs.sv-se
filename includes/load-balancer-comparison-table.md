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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202472"
---
| | Standard Load Balancer | Grundläggande Load Balancer |
| --- | --- | --- |
| [Storlek på backend-pool](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Stöder upp till 1000 instanser. | Har stöd för upp till 300 instanser. |
| Slutpunkter för serverdelspool | Virtuella datorer eller skalnings uppsättningar för virtuella datorer i ett enda virtuellt nätverk. | Virtuella datorer i en enskild tillgänglighetsuppsättning eller en VM-skalningsuppsättning. |
| [Hälsoavsökningar](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Beteende hos hälsoavsökning ur funktion](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-anslutningar är aktiva vid en instans avsökning __och__ i alla avsökningar. | TCP-anslutningar är vilande vid en instans avsökning. Alla TCP-anslutningar avslutas när alla avsökningar är nere. |
| Tillgänglighetszoner | Zoner-redundanta och zonindelade-frontend-klienter för inkommande och utgående trafik. | Inte tillgängligt |
| Diagnostik | [Azure Monitor flerdimensionella mått](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Azure Monitor-loggar](../articles/load-balancer/load-balancer-monitor-log.md) |
| HA-portar | [Tillgängligt för interna Load Balancer](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Inte tillgängligt |
| Säker som standard | Stängda för inkommande flöden om det inte tillåts av en nätverks säkerhets grupp. Observera att intern trafik från VNet till den interna belastningsutjämnaren tillåts. | Öppnas som standard. Nätverks säkerhets gruppen är valfri. |
| Regler för utgående trafik | [Deklarativ utgående NAT-konfiguration](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Inte tillgängligt |
| TCP-återställning vid inaktivitet | [Tillgängligt för någon regel](../articles/load-balancer/load-balancer-tcp-reset.md) | Inte tillgängligt |
| [Flera frontend-sidor](../articles/load-balancer/load-balancer-multivip-overview.md) | Inkommande och [utgående](../articles/load-balancer/load-balancer-outbound-connections.md) | Endast inkommande |
| Hanteringsåtgärder | De flesta åtgärder < 30 sekunder | 60-90 + sekunders standard |
| SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Inte tillgängligt | 
