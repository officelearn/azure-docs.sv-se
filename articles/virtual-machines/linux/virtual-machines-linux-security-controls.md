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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080071"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Säkerhets kontroller för Virtuella Linux-datorer

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Virtuella Linux-datorer.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Stöd för tjänst slut punkt| Ja | |
| Stöd för VNet-injektering| Ja | |
| Stöd för nätverks isolering och brand vägg| Ja |  |
| Stöd för Tvingad tunnel trafik| Ja | Se [Konfigurera Tvingad tunnel trafik med Azure Resource Manager distributions modell](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [övervaka och uppdatera en virtuell Linux-dator i Azure](./tutorial-monitor.md). |
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |
| Loggning och granskning av data planet | Inga |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Autentisering| Ja |  |
| Auktorisering| Ja |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Se [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-overview.md). |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet kryptering)| Ja | Azure Virtual Machines stöder [ExpressRoute](../../expressroute/index.yml) och VNET-kryptering. Se [kryptering under överföring i virtuella datorer](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja | Kundhanterade nycklar är ett Azure-krypterings scenario som stöds. Se [Översikt över Azure-kryptering](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms).|
| Kryptering på kolumn nivå (Azure Data Services)| E.t. | |
| Krypterade API-anrop| Ja | Via HTTPS och TLS. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja |  | 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../../security/fundamentals/security-controls.md).
