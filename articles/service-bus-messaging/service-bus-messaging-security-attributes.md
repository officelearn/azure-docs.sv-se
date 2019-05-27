---
title: Vanliga säkerhetsattribut för Azure Service Bus-meddelanden
description: En lista med vanliga security attribut för att utvärdera Azure Service Bus-meddelanden
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d68ffe6561da6a23c288dfabd1d3eb6b34099bb3
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003117"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Security attribut för Azure Service Bus-meddelanden

Den här artikeln dokumenteras security-attribut som är inbyggda i Azure Service Bus-meddelanden.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>|  Ja för serversidan kryptering vid vila som standard. | Kundhanterade nycklar och BYOK stöds inte ännu. Kryptering är klientens ansvar |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | Stöder standard HTTPS/TLS-mekanism. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Nej |   |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | API-anrop som görs via [Azure Resource Manager](../azure-resource-manager/index.yml) och HTTPS. |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja (endast Premium-nivån) | Tjänstslutpunkter i virtuella nätverk stöds för [Service Bus Premium-nivån](service-bus-premium-messaging.md) endast. |
| VNet-stöd för inmatning| Nej | |
| Isolering av nätverk och brandväggsfunktioner support| Ja (endast Premium-nivån) |  |
| Tvingad tunneltrafik support| Nej |  |

## <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Stöds via [Azure Monitor och aviseringar](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Hanteras via [Azure Active Directory hanterad tjänstidentitet](service-bus-managed-service-identity.md); Se [Service Bus, autentisering och auktorisering](service-bus-authentication-and-authorization.md).|
| Auktorisering| Ja | Har stöd för auktorisering via [RBAC](service-bus-role-based-access-control.md) (förhandsversion) och SAS-token, se [Service Bus, autentisering och auktorisering](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Åtgärdsloggar är tillgängliga. Se [Service Bus diagnostikloggar](service-bus-diagnostic-logs.md).  |
| Data plan loggning och granskning| Nej |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Har stöd för resource provider versionshantering via den [Azure Resource Manager API](/rest/api/resources/).|
