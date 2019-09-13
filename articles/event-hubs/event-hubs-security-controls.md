---
title: Säkerhets kontroller för Azure Event Hubs
description: En check lista över säkerhets kontroller för utvärdering av Azure-Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 3f02315012840b20dc4aa8639ade954f23a5526e
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886766"
---
# <a name="security-controls-for-azure-event-hubs"></a>Säkerhets kontroller för Azure Event Hubs

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Nej | |
| Stöd för nätverks isolering och brand vägg| Ja |  |
| Stöd för Tvingad tunnel trafik| Nej |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | |
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |
| Loggning och granskning av data planet| Ja |   |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | |
| Authorization|  Ja | |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan på rest: Microsoft-hanterade nycklar |  Ja | |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Nej |  |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | |
| Krypterade API-anrop| Ja |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
