---
title: 'Azure-ExpressRoute: säkerhets kontroller'
description: En check lista över säkerhets kontroller för utvärdering av Azure-ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74079936"
---
# <a name="security-controls-for-azure-expressroute"></a>Säkerhets kontroller för Azure-ExpressRoute

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure dataExpressRoutes.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Stöd för tjänst slut punkt| E.t. |  |
| Stöd för VNet-injektering| E.t. | |
| Stöd för nätverks isolering och brand vägg| Ja | Varje kund ingår i en egen routningsdomän och dirigeras sedan till sitt eget VNet |
| Stöd för Tvingad tunnel trafik| E.t. | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [ExpressRoute-övervakning, mått och aviseringar](expressroute-monitoring-metrics-alerts.md).|
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |
| Loggning och granskning av data planet| No |   |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Autentisering| Ja | Tjänst konto för gateway för Microsoft (GWM) (kontrollant); JIT-åtkomst (just in Time) för utveckling och OP. |
| Auktorisering|  Ja |Tjänst konto för gateway för Microsoft (GWM) (kontrollant); JIT-åtkomst (just in Time) för utveckling och OP. |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar |  E.t. | ExpressRoute lagrar inte kund information. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | E.t. |  |
| Kryptering på kolumn nivå (Azure Data Services)| E.t. | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| No | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) och https. |


## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Via nätverks resurs leverantören (NRP). |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).