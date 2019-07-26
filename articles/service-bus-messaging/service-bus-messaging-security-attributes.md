---
title: Säkerhetsattribut för Azure Service Bus meddelande funktion
description: En check lista över säkerhetsattribut för utvärdering av Azure Service Bus meddelanden
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e1d6e041b47a261b549fb8b608cf09d0d6362dd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443904"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Säkerhetsattribut för Azure Service Bus meddelande funktion

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure Service Bus Messaging.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner)|  Ja för kryptering på Server sidan – rest som standard. | Kundhanterade nycklar och BYOK stöds inte ännu. Kryptering på klient sidan är klientens ansvar |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Stöder standard-HTTPS/TLS-mekanismen. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej |   |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | API-anrop görs via [Azure Resource Manager](../azure-resource-manager/index.yml) och https. |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja (endast Premium-nivå) | VNet-tjänstens slut punkter stöds endast för [Service Bus Premium-nivån](service-bus-premium-messaging.md) . |
| Stöd för VNet-injektering| Nej | |
| Stöd för nätverks isolering och brand vägg| Ja (endast Premium-nivå) |  |
| Stöd för Tvingad tunnel trafik| Nej |  |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Stöds via [Azure Monitor och aviseringar](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Hanteras via [Azure Active Directory hanterad tjänstidentitet](service-bus-managed-service-identity.md); Se [Service Bus autentisering och auktorisering](service-bus-authentication-and-authorization.md).|
| Authorization| Ja | Stöder auktorisering via [RBAC](service-bus-role-based-access-control.md) (för hands version) och SAS-token; Se [Service Bus autentisering och auktorisering](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Det finns tillgängliga åtgärds loggar. Se [Service Bus diagnostikloggar](service-bus-diagnostic-logs.md).  |
| Loggning och granskning av data planet| Nej |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Stöder versions hantering av resurs leverantörer via [Azure Resource Manager API](/rest/api/resources/).|
