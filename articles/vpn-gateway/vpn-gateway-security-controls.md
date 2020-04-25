---
title: Säkerhets kontroller för Azure VPN Gateway
description: En check lista över säkerhets kontroller för utvärdering av Azure-VPN Gateway
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fc5b4c901254decdb2d34281a10ababd4d79d45
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127858"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Säkerhets kontroller för Azure VPN Gateway

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ej tillämpligt | |
| Stöd för VNet-injektering| Ej tillämpligt | |
| Stöd för nätverks isolering och brand vägg| Ja | VPN-gatewayer är dedikerade VM-instanser för varje kund Virtual Network  |
| Stöd för Tvingad tunnel trafik| Ja |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se varning för [Azure Monitor logg avisering](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor mått](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Azure Resource Manager aktivitets logg. |
| Loggning och granskning av data planet | Ja | [Azure Monitor aktivitets loggar](../azure-resource-manager/management/view-activity-logs.md) för loggning och granskning av VPN-anslutning. |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) för att hantera tjänsten och konfigurera Azure VPN-gatewayen. |
| Auktorisering| Ja | Stöd för auktorisering via [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ej tillämpligt | Kunddata för VPN gateway-överföring lagrar inte kund information |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | VPN gateway krypterar kund paket mellan Azure VPN-gatewayer och lokala VPN-enheter (S2S) eller VPN-klienter (P2S). VPN-gatewayer stöder även VNet-till-VNet-kryptering. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Inga | Kundspecificerade i förväg delade nycklar är krypterade i vila. men inte integrerat med CMK än. |
| Kryptering på kolumn nivå (Azure Data Services)| Ej tillämpligt | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) och https  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | För hanterings åtgärder kan statusen för en Azure VPN gateway-konfiguration exporteras som en Azure Resource Manager mall och versions hantering över tid. |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
