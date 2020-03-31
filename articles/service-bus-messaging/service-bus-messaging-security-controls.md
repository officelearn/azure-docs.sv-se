---
title: Säkerhetskontroller för Azure Service Bus Messaging
description: En checklista över säkerhetskontroller för utvärdering av Azure Service Bus Messaging
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75903254"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Säkerhetskontroller för Azure Service Bus Messaging

Den här artikeln dokumenterar de säkerhetskontroller som är inbyggda i Azure Service Bus Messaging.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Support för tjänstens slutpunkt| Ja (endast Premium-nivå) | Slutpunkter för VNet-tjänsten stöds endast för [Service Bus Premium-nivån.](service-bus-premium-messaging.md) |  |
| Stöd för VNet-injektion| Inga | |  |
| Stöd för nätverksisolering och brandväggar| Ja (endast Premium-nivå) |  |  |
| Stöd för påtvingad tunnelning| Inga |  |  |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | Stöds via [Azure Monitor och aviseringar](service-bus-metrics-azure-monitor.md). |  |
| Kontroll- och hanteringsplan loggning och revision| Ja | Operationsloggar är tillgängliga.  | [Diagnostikloggar för Service Bus](service-bus-diagnostic-logs.md) |
| Loggning och granskning av dataplan| Inga |  |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Autentisering| Ja | Hanteras via [Azure Active Directory Managed Service Identity](service-bus-managed-service-identity.md).| [Autentisering och auktorisering för servicebuss](service-bus-authentication-and-authorization.md). |
| Auktorisering| Ja | Stöder auktorisering via [RBAC-](authenticate-application.md) och SAS-token. | [Autentisering och auktorisering för servicebuss](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar |  Ja för kryptering på serversidan som standard. |  |  |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ja. | En kundhanterad nyckel i Azure KeyVault kan användas för att kryptera data på Service Bus Namespace i vila. | [Konfigurera kundhanterade nycklar för kryptering av Azure Service Bus-data i vila med hjälp av Azure-portalen](configure-customer-managed-key.md)  |
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt | |   |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | Stöder standard-HTTPS/TLS-mekanism. |   |
| API-anrop krypterade| Ja | API-anrop görs via [Azure Resource Manager](../azure-resource-manager/index.yml) och HTTPS. |   |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja | Stöder resursproviderversionering via [Azure Resource Manager API](/rest/api/resources/).|   |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).
