---
title: Säkerhets kontroller för Azure Relay
description: I den här artikeln finns en check lista över inbyggda säkerhets kontroller för utvärdering av Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 64abee031bb20e2bdb10bf1cc3cd77e135713550
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85316614"
---
# <a name="security-controls-for-azure-relay"></a>Säkerhets kontroller för Azure Relay

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation |
|---|---|--|--|
| Stöd för tjänst slut punkt| No |  |   |
| Stöd för nätverks isolering och brand vägg| No |  |   |
| Stöd för Tvingad tunnel trafik| Ej tillämpligt | Relä är TLS-tunneln  |   |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation |
|---|---|--|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Yes | |   |
| Loggning och granskning av kontroll-och hanterings plan| Yes | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Loggning och granskning av data planet| Yes | Lyckad/misslyckad anslutning och fel och loggad.  |   |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation |
|---|---|--|--|
| Autentisering| Yes | Via SAS. | [Azure Relay autentisering och auktorisering](relay-authentication-and-authorization.md) |
| Auktorisering|  Yes | Via SAS. | [Azure Relay autentisering och auktorisering](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation |
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar |  Ej tillämpligt | Relay är en webbsocket och bevarar inte data. |   |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | No | Använder endast Microsoft TLS-certifikat.  |   |
| Kryptering på kolumn nivå (Azure Data Services)| Ej tillämpligt | |   |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Yes | Tjänsten kräver TLS. |   |
| Krypterade API-anrop| Yes | HTTPS. |


## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation |
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Yes | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).