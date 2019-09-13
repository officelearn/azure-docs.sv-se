---
title: Säkerhets kontroller för Azure-ExpressRoute
description: En check lista över säkerhets kontroller för utvärdering av Azure-ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ab73ba6dd4b78d3cd0be5f4c7f7a502e5c58e08
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886542"
---
# <a name="security-controls-for-azure-expressroute"></a>Säkerhets kontroller för Azure-ExpressRoute

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure dataExpressRoutes.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Gäller inte |  |
| Stöd för VNet-injektering| Gäller inte | |
| Stöd för nätverks isolering och brand vägg| Ja | Varje kund ingår i en egen routningsdomän och dirigeras sedan till sitt eget VNet |
| Stöd för Tvingad tunnel trafik| Gäller inte | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [ExpressRoute-övervakning, mått och aviseringar](expressroute-monitoring-metrics-alerts.md).|
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |
| Loggning och granskning av data planet| Nej |   |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Tjänst konto för gateway för Microsoft (GWM) (kontrollant); JIT-åtkomst (just in Time) för utveckling och OP. |
| Authorization|  Ja |Tjänst konto för gateway för Microsoft (GWM) (kontrollant); JIT-åtkomst (just in Time) för utveckling och OP. |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan på rest: Microsoft-hanterade nycklar |  Gäller inte | ExpressRoute lagrar inte kund information. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Gäller inte |  |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Nej | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) och https. |


## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Via nätverks resurs leverantören (NRP). |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).