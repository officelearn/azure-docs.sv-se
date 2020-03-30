---
title: Säkerhetskontroller för Azure Load Balancer
description: En checklista över säkerhetskontroller för utvärdering av belastningsutjämnare
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214905"
---
# <a name="security-controls-for-azure-load-balancer"></a>Säkerhetskontroller för Azure Load Balancer

Den här artikeln dokumenterar de säkerhetskontroller som är inbyggda i Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Support för tjänstens slutpunkt| Ej tillämpligt | |
| Stöd för VNet-injektion| Ej tillämpligt | |
| Stöd för nätverksisolering och brandväggar| Ej tillämpligt |  |
| Stöd för påtvingad tunnelning| Ej tillämpligt | |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | Se [Azure Monitor-loggar för offentliga grundläggande belastningsutjämnare](load-balancer-monitor-log.md). |
| Kontroll- och hanteringsplan loggning och revision| Ja | Se [Azure Monitor-loggar för offentliga grundläggande belastningsutjämnare](load-balancer-monitor-log.md). |
| Loggning och granskning av dataplan | Ej tillämpligt |  |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ej tillämpligt |  |
| Auktorisering| Ej tillämpligt |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ej tillämpligt | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ej tillämpligt | |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ej tillämpligt | |
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt | |
| API-anrop krypterade| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ej tillämpligt |  | 

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).