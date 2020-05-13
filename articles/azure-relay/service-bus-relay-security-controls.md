---
title: Säkerhets kontroller för Azure Relay
description: I den här artikeln finns en check lista över inbyggda säkerhets kontroller för utvärdering av Azure Relay.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: f8165d994e998af4f15cd6aa2fd08b75191b8b64
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211466"
---
# <a name="security-controls-for-azure-relay"></a>Säkerhets kontroller för Azure Relay

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Stöd för tjänst slut punkt| Inga |  |   |
| Stöd för nätverks isolering och brand vägg| Inga |  |   |
| Stöd för Tvingad tunnel trafik| E.t. | Relä är TLS-tunneln  |   |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | |   |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Loggning och granskning av data planet| Ja | Lyckad/misslyckad anslutning och fel och loggad.  |   |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Autentisering| Ja | Via SAS. | [Azure Relay autentisering och auktorisering](relay-authentication-and-authorization.md) |
| Auktorisering|  Ja | Via SAS. | [Azure Relay autentisering och auktorisering](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar |  E.t. | Relay är en webbsocket och bevarar inte data. |   |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Inga | Använder endast Microsoft TLS-certifikat.  |   |
| Kryptering på kolumn nivå (Azure Data Services)| E.t. | |   |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Tjänsten kräver TLS. |   |
| Krypterade API-anrop| Ja | HTTPS. |


## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).