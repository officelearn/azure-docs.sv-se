---
title: Säkerhets kontroller för Azure Service Bus meddelanden
description: En check lista över säkerhets kontroller för utvärdering av Azure Service Bus meddelanden
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3130150a227076befae3f58f65e00a36578b68d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341633"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Säkerhets kontroller för Azure Service Bus meddelanden

I den här artikeln dokumenteras säkerhets kontroller som är inbyggda i Azure Service Bus meddelanden.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation |
|---|---|--|--|
| Stöd för tjänst slut punkt| Ja (endast Premium-nivå) | VNet-tjänstens slut punkter stöds endast för [Service Bus Premium-nivån](service-bus-premium-messaging.md) . |  |
| Stöd för VNet-injektering| No | |  |
| Stöd för nätverks isolering och brand vägg| Ja (endast Premium-nivå) |  |  |
| Stöd för Tvingad tunnel trafik| No |  |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Obs!| Dokumentation |
|---|---|--|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Stöds via [Azure Monitor och aviseringar](service-bus-metrics-azure-monitor.md). |  |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Det finns tillgängliga åtgärds loggar.  | [Service Bus diagnostikloggar](service-bus-diagnostic-logs.md) |
| Loggning och granskning av data planet| No |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Obs!| Dokumentation |
|---|---|--|--|
| Autentisering| Ja | Hanteras via [Azure Active Directory hanterad tjänstidentitet](service-bus-managed-service-identity.md).| [Service Bus autentisering och auktorisering](service-bus-authentication-and-authorization.md). |
| Auktorisering| Ja | Stöder auktorisering via [RBAC](authenticate-application.md) och SAS-token. | [Service Bus autentisering och auktorisering](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation |
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar |  Ja för kryptering på Server sidan – rest som standard. |  |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja. | En kundhanterad nyckel i Azure-valv kan användas för att kryptera data i namn området Service Bus i vila. | [Konfigurera Kundhanterade nycklar för att kryptera Azure Service Bus data i vila med hjälp av Azure Portal](configure-customer-managed-key.md)  |
| Kryptering på kolumn nivå (Azure Data Services)| E.t. | |   |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Stöder standard-HTTPS/TLS-mekanismen. |   |
| Krypterade API-anrop| Ja | API-anrop görs via [Azure Resource Manager](../azure-resource-manager/index.yml) och https. |   |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Obs!| Dokumentation |
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Stöder versions hantering av resurs leverantörer via [Azure Resource Manager API](/rest/api/resources/).|   |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
