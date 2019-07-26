---
title: Säkerhetsattribut för Azure VPN Gateway
description: En check lista över säkerhetsattribut för utvärdering av Azure-VPN Gateway
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: c355f70975f441609304a4c9ee2ead75f0e0ce25
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444588"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Säkerhetsattribut för Azure VPN Gateway

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure VPN Gateway.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Gäller inte | Kunddata för VPN gateway-överföring lagrar inte kund information |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | VPN gateway krypterar kund paket mellan Azure VPN-gatewayer och lokala VPN-enheter (S2S) eller VPN-klienter (P2S). VPN-gatewayer stöder även VNet-till-VNet-kryptering. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej | Kundspecificerade i förväg delade nycklar är krypterade i vila. men inte integrerat med CMK än. |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) och https  |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Gäller inte | |
| Stöd för VNet-injektering| Gäller inte | . |
| Stöd för nätverks isolering och brand vägg| Ja | VPN-gatewayer är dedikerade VM-instanser för varje kund Virtual Network  |
| Stöd för Tvingad tunnel trafik| Ja |  |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [Azure Monitor diagnostikloggar/varning](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor mått/avisering](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) för att hantera tjänsten och konfigurera Azure VPN-gatewayen. |
| Authorization| Ja | Stöd för auktorisering via [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Azure Resource Manager aktivitets logg. |
| Loggning och granskning av data planet | Ja | [Azure Monitor diagnostikloggar](../azure-resource-manager/resource-group-audit.md) för loggning och granskning av VPN-anslutningar. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | För hanterings åtgärder kan statusen för en Azure VPN gateway-konfiguration exporteras som en Azure Resource Manager mall och versions hantering över tid. | 