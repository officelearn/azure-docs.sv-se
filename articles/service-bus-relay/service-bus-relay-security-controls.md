---
title: Säkerhetskontroller för Azure Service Bus Relay
description: Den här artikeln innehåller en checklista med inbyggda säkerhetskontroller för utvärdering av Azure Service Bus Relay.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514025"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Säkerhetskontroller för Azure Service Bus Relay

Den här artikeln dokumenterar de säkerhetskontroller som är inbyggda i Azure Service Bus Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Support för tjänstens slutpunkt| Inga |  |   |
| Stöd för nätverksisolering och brandväggar| Inga |  |   |
| Stöd för påtvingad tunnelning| Ej tillämpligt | Relä är TLS-tunneln  |   |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | |   |
| Kontroll- och hanteringsplan loggning och revision| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Loggning och granskning av dataplan| Ja | Anslutningsframgång/fel och fel och loggade.  |   |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Autentisering| Ja | Via SAS. | [Azure Relay-autentisering och auktorisering](relay-authentication-and-authorization.md) |
| Auktorisering|  Ja | Via SAS. | [Azure Relay-autentisering och auktorisering](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar |  Ej tillämpligt | Relay är en webbsockel och beständiga data. |   |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Inga | Använder endast Microsoft TLS-certifikat.  |   |
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt | |   |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | Tjänsten kräver TLS. |   |
| API-anrop krypterade| Ja | HTTPS. |


## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).