---
title: Säkerhets kontroller för Azure Load Balancer
description: En check lista över säkerhets kontroller för utvärdering av Load Balancer
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e0be6635a0916183e1dfe776bef4c547578383dc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886837"
---
# <a name="security-controls-for-azure-load-balancer"></a>Säkerhets kontroller för Azure Load Balancer

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Gäller inte | |
| Stöd för VNet-injektering| Gäller inte | |
| Stöd för nätverks isolering och brand vägg| Gäller inte |  |
| Stöd för Tvingad tunnel trafik| Gäller inte | |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [Azure Monitor-loggar för offentliga grundläggande Load Balancer](load-balancer-monitor-log.md). |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Se [Azure Monitor-loggar för offentliga grundläggande Load Balancer](load-balancer-monitor-log.md). |
| Loggning och granskning av data planet | Gäller inte |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Gäller inte |  |
| Authorization| Gäller inte |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan på rest: Microsoft-hanterade nycklar | Gäller inte | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Gäller inte | |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Gäller inte | |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Gäller inte |  | 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).