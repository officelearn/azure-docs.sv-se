---
title: 'Azure ExpressRoute: Säkerhetskontroller'
description: En checklista över säkerhetskontroller för utvärdering av Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079936"
---
# <a name="security-controls-for-azure-expressroute"></a>Säkerhetskontroller för Azure ExpressRoute

Den här artikeln dokumenterar säkerhetskontrollerna som är inbyggda i Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Support för tjänstens slutpunkt| Ej tillämpligt |  |
| Stöd för VNet-injektion| Ej tillämpligt | |
| Stöd för nätverksisolering och brandväggar| Ja | Varje kund finns i sin egen routningsdomän och tunneleras till sitt eget virtuella nätverk |
| Stöd för påtvingad tunnelning| Ej tillämpligt | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | Se [ExpressRoute-övervakning, mått och aviseringar](expressroute-monitoring-metrics-alerts.md).|
| Kontroll- och hanteringsplan loggning och revision| Ja |  |
| Loggning och granskning av dataplan| Inga |   |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Tjänstkonto för Gateway för Microsoft (GWM) (controller); Just in Time (JIT) åtkomst för Dev och OP. |
| Auktorisering|  Ja |Tjänstkonto för Gateway för Microsoft (GWM) (controller); Just in Time (JIT) åtkomst för Dev och OP. |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar |  Ej tillämpligt | ExpressRoute lagrar inte kunddata. |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ej tillämpligt |  |
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Inga | |
| API-anrop krypterade| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) och HTTPS. |


## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja | Via leverantören av nätverksresurser (NRP). |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).