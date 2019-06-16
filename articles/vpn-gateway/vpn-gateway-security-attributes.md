---
title: Security attribut för Azure VPN Gateway
description: En checklista med säkerhetsattribut för att utvärdera Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083137"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Security attribut för Azure VPN Gateway

Den här artikeln beskrivs vanliga security attribut som är inbyggda i Azure VPN Gateway.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t.ex kryptering på serversidan, kryptering på serversidan med Kundhanterade nycklar och andra krypteringsfunktioner) | Gäller inte | Kundinformation för VPN-gateway överföring, lagrar inte kunddata |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i virtuella nätverk, och kryptering för VNet-VNet)| Ja | VPN-gateway kryptera kunden paket mellan Azure VPN-gatewayer och kundens lokala VPN-enheter (S2S) eller VPN-klienter (P2S). VPN-gatewayer har också stöd för VNet-till-VNet-kryptering. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Nej | Kunden angivna i förväg delade nycklarna krypteras i vila; men inte integrerad med CMK ännu. |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | Via [med Azure Resource Manager](../azure-resource-manager/index.yml) och HTTPS  |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Gäller inte | |
| VNet-stöd för inmatning| Gäller inte | . |
| Stöd för isolering av nätverk och Firewalling| Ja | VPN-gatewayer är dedikerade VM-instanser för varje kund virtuellt nätverk  |
| Tvingad tunneltrafik support| Ja |  |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Se [Azure Monitor-loggar/diagnosaviseringen](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor/avisering](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) för att hantera tjänsten och konfigurera Azure VPN-gatewayen. |
| Auktorisering| Ja | Stöd för auktorisering via [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Azure Resource Manager-aktivitetsloggen. |
| Data plan loggning och granskning | Ja | [Azure Monitor-diagnostikloggar](../azure-resource-manager/resource-group-audit.md) för VPN-anslutning loggning och granskning. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | För hanteringsåtgärder, kan tillståndet för en Azure VPN gateway-konfiguration exporteras som en Azure Resource Manager-mall och version över tid. | 