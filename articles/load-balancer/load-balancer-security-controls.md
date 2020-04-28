---
title: Säkerhets kontroller för Azure Load Balancer
description: En check lista över säkerhets kontroller för utvärdering av Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74214905"
---
# <a name="security-controls-for-azure-load-balancer"></a>Säkerhets kontroller för Azure Load Balancer

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ej tillämpligt | |
| Stöd för VNet-injektering| Ej tillämpligt | |
| Stöd för nätverks isolering och brand vägg| Ej tillämpligt |  |
| Stöd för Tvingad tunnel trafik| Ej tillämpligt | |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [Azure Monitor-loggar för offentliga grundläggande Load Balancer](load-balancer-monitor-log.md). |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Se [Azure Monitor-loggar för offentliga grundläggande Load Balancer](load-balancer-monitor-log.md). |
| Loggning och granskning av data planet | Ej tillämpligt |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ej tillämpligt |  |
| Auktorisering| Ej tillämpligt |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ej tillämpligt | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ej tillämpligt | |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ej tillämpligt | |
| Kryptering på kolumn nivå (Azure Data Services)| Ej tillämpligt | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ej tillämpligt |  | 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).