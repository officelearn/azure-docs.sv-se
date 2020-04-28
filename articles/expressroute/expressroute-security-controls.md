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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74079936"
---
# <a name="security-controls-for-azure-expressroute"></a>Säkerhets kontroller för Azure-ExpressRoute

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure dataExpressRoutes.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ej tillämpligt |  |
| Stöd för VNet-injektering| Ej tillämpligt | |
| Stöd för nätverks isolering och brand vägg| Ja | Varje kund ingår i en egen routningsdomän och dirigeras sedan till sitt eget VNet |
| Stöd för Tvingad tunnel trafik| Ej tillämpligt | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [ExpressRoute-övervakning, mått och aviseringar](expressroute-monitoring-metrics-alerts.md).|
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |
| Loggning och granskning av data planet| Inga |   |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Tjänst konto för gateway för Microsoft (GWM) (kontrollant); JIT-åtkomst (just in Time) för utveckling och OP. |
| Auktorisering|  Ja |Tjänst konto för gateway för Microsoft (GWM) (kontrollant); JIT-åtkomst (just in Time) för utveckling och OP. |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar |  Ej tillämpligt | ExpressRoute lagrar inte kund information. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ej tillämpligt |  |
| Kryptering på kolumn nivå (Azure Data Services)| Ej tillämpligt | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Inga | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) och https. |


## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Via nätverks resurs leverantören (NRP). |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).