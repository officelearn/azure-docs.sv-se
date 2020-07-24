---
title: Säkerhets kontroller för Azure Virtuella Linux-datorer – Linux
description: En check lista över säkerhets kontroller för utvärdering av Azure-Virtuella Linux-datorer
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 132f696dd1298384c302eeadc264e857d1edff96
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080071"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Säkerhets kontroller för Virtuella Linux-datorer

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Virtuella Linux-datorer.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Kommentarer |
|---|---|--|
| Stöd för tjänst slut punkt| Yes | |
| Stöd för VNet-injektering| Yes | |
| Stöd för nätverks isolering och brand vägg| Yes |  |
| Stöd för Tvingad tunnel trafik| Yes | Se [Konfigurera Tvingad tunnel trafik med Azure Resource Manager distributions modell](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Kommentarer|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Yes | Se [övervaka och uppdatera en virtuell Linux-dator i Azure](./tutorial-monitor.md). |
| Loggning och granskning av kontroll-och hanterings plan| Yes |  |
| Loggning och granskning av data planet | No |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Kommentarer|
|---|---|--|
| Autentisering| Yes |  |
| Auktorisering| Yes |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Kommentarer |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Yes | Se [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-overview.md). |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Yes | Azure Virtual Machines stöder [ExpressRoute](../../expressroute/index.yml) och VNET-kryptering. Se [kryptering under överföring i virtuella datorer](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Yes | Kundhanterade nycklar är ett Azure-krypterings scenario som stöds. Se [Översikt över Azure-kryptering](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms).|
| Kryptering på kolumn nivå (Azure Data Services)| Ej tillämpligt | |
| Krypterade API-anrop| Yes | Via HTTPS och TLS. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Kommentarer|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja |  | 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../../security/fundamentals/security-controls.md).
