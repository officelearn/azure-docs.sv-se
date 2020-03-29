---
title: Säkerhetskontroller för Azure VPN Gateway
description: En checklista över säkerhetskontroller för utvärdering av Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972283"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Säkerhetskontroller för Azure VPN Gateway

Den här artikeln dokumenterar säkerhetskontrollerna som är inbyggda i Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Support för tjänstens slutpunkt| Ej tillämpligt | |
| Stöd för VNet-injektion| Ej tillämpligt | |
| Stöd för nätverksisolering och brandväggar| Ja | VPN-gateways är dedikerade VM-instanser för varje kund virtuellt nätverk  |
| Stöd för påtvingad tunnelning| Ja |  |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | Se [Azure Monitor Diagnostics Loggar/avisera](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor-mått/avisering](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Kontroll- och hanteringsplan loggning och revision| Ja | Aktivitetsloggen för Azure Resource Manager. |
| Loggning och granskning av dataplan | Ja | [Diagnostikloggar för Azure Monitor](../azure-resource-manager/management/view-activity-logs.md) för VPN-anslutningsloggning och granskning. |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) för hantering av tjänsten och konfigurera Azure VPN-gatewayen. |
| Auktorisering| Ja | Supportauktorisering via [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ej tillämpligt | VPN gateway transit kunddata, lagrar INTE kunddata |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | VPN-gateway kryptera kundpaket mellan Azure VPN-gateways och kundens lokala VPN-enheter (S2S) eller VPN-klienter (P2S). VPN-gateways stöder också VNet-till-VNet-kryptering. |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Inga | Kundspecificerade fördelade nycklar krypteras i vila. men inte integrerat med CMK ännu. |
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt | |
| API-anrop krypterade| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) och HTTPS  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja | För hanteringsåtgärder kan tillståndet för en Azure VPN-gatewaykonfiguration exporteras som en Azure Resource Manager-mall och versionsas med tiden. |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).
